---
title: 串口工具
date: 2019-07-15 14:26:28
comments: true
tags: 串口工具
---

## 完整代码
```
import android.text.TextUtils;
import android.util.Log;

import com.deemons.serialportlib.SerialPort;
import com.glodon.tool.DataUtils;

import java.io.IOException;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

/**
 * 串口帮助类
 * <p>
 * 使用简述
 * <p>
 * 添加库：implementation "com.deemons.serialport:serialport:1.2.0"
 *
 * @see
 * <a href=“https://github.com/Deemonser/AndroidSerialPort”>https://github.com/Deemonser/AndroidSerialPort</a>
 * <p>
 * @see
 * <a href=“http://pm.glodon.com/newjira/browse/GLW-1234”>通信协议：http://pm.glodon.com/newjira/browse/GLW-1234</a>
 * <p>
 * Created by lichongmac@163.com on 2019-07-12.
 */
public class SerialPortHelper {
    private static final String TAG = SerialPortHelper.class.getSimpleName();

    private SerialPort mSerialPort;

    private String filePath = "/dev/ttyS2";
    private int baudRate = 115200;

    private static SerialPortHelper sSerialPortHelper;

    private SerialPortHelper() {
        openSerialPort();
    }

    public void openSerialPort() {
        closeSerialPort();
        try {
            mSerialPort = new SerialPort(filePath, baudRate);
        } catch (IOException e) {
            e.printStackTrace();
        }

        new ReceiveThread().start();
    }

    public static SerialPortHelper getInstance() {
        if (sSerialPortHelper == null) {
            sSerialPortHelper = new SerialPortHelper();
        }
        return sSerialPortHelper;
    }

    public void closeSerialPort() {
        if (mSerialPort != null) {
            isStart = false;
            mSerialPort.close();
        }
    }

    private boolean isStart = false;

    /**
     * 接收串口数据的线程
     */
    private class ReceiveThread extends Thread {

        public ReceiveThread() {
            isStart = true;
        }

        private String dataBuffer = "";
        private String CMD_HEADER = "BB";

        @Override
        public void run() {
            super.run();
            //条件判断，只要条件为true，则一直执行这个线程
            while (isStart) {
                if (mSerialPort.getInputStream() == null) {
                    Log.e(TAG, "数据流出现未知异常");
                    return;
                }

                byte[] readData = new byte[1024];
                try {
                    int size = mSerialPort.getInputStream().read(readData);
                    if (size > 0) {

                        String data = DataUtils.ByteArrToHex(readData, 0, size);
                        if (!TextUtils.isEmpty(data)) {
                            Log.i(TAG, data);
                            dataBuffer += data;
                        }
                        while (dataBuffer.contains(CMD_HEADER) && dataBuffer.length() > 10) {
//                            Log.i(TAG, "start dataBuffer = " + dataBuffer);
                            int start = dataBuffer.indexOf(CMD_HEADER) + CMD_HEADER.length();
                            String msgType = dataBuffer.substring(start, start + 2);
                            String msgComm = dataBuffer.substring(start + 2, start + 4);
                            String contentLength = dataBuffer.substring(start + 4, start + 8);

                            int length = DataUtils.HexToInt(contentLength);

                            if (start + 8 + length * 2 > dataBuffer.length())
                                break;

                            String content = dataBuffer.substring(start + 8,
                                    start + 8 + length * 2);
                            Log.i(TAG, "消息类型：" + msgType
                                    + "，指令：" + msgComm
                                    + "，数据长度：" + length
                                    + "，content：" + content
                            );
                            //通知观察者或监听者消息内容
                            SerialPortHelper.getInstance().notify(msgType, msgComm, content);

                            dataBuffer = dataBuffer.substring(start + 8 + length * 2);
                            if (dataBuffer.contains(CMD_HEADER)) {//剔除尾部和校验位
                                dataBuffer = dataBuffer.substring(dataBuffer.indexOf(CMD_HEADER));
                            }
//                            Log.i(TAG, "end dataBuffer = " + dataBuffer);
                        }
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }

    public interface SerialPortListener {
        void onReceive(String msgType, String msgComm, String content);
    }

    List<SerialPortListener> mListeners = new CopyOnWriteArrayList();

    public void addListener(SerialPortListener listener) {
        if (!mListeners.contains(listener)) {
            mListeners.add(listener);
        }
    }

    public void removeListener(SerialPortListener listener) {
        if (mListeners.contains(listener)) {
            mListeners.remove(listener);
        }
    }

    public void notify(String msgType, String msgComm, String content) {
        for (SerialPortListener listener : mListeners)
            listener.onReceive(msgType, msgComm, content);
    }
}

```
### 辅助类
```
import android.text.TextUtils;

import java.util.ArrayList;
import java.util.List;

/**
 * 串口数据转换工具类
 * Created by Administrator on 2016/6/2.
 */
public class DataUtils {
    //-------------------------------------------------------
    // 判断奇数或偶数，位运算，最后一位是1则为奇数，为0是偶数
    public static int isOdd(int num) {
        return num & 1;
    }

    //-------------------------------------------------------
    //Hex字符串转int
    public static int HexToInt(String inHex) {
        if(TextUtils.isEmpty(inHex)) {
            return 0;
        }
        try {

        return Integer.parseInt(inHex, 16);
        }catch (NumberFormatException e){
            e.printStackTrace();
        }
        return 0;
    }

    public static String IntToHex(int intHex){
        return Integer.toHexString(intHex);
    }

    //-------------------------------------------------------
    //Hex字符串转byte
    public static byte HexToByte(String inHex) {
        return (byte) Integer.parseInt(inHex, 16);
    }

    //-------------------------------------------------------
    //1字节转2个Hex字符
    public static String Byte2Hex(Byte inByte) {
        return String.format("%02x", new Object[]{inByte}).toUpperCase();
    }

    //-------------------------------------------------------
    //字节数组转转hex字符串
    public static String ByteArrToHex(byte[] inBytArr) {
        StringBuilder strBuilder = new StringBuilder();
        for (byte valueOf : inBytArr) {
            strBuilder.append(Byte2Hex(Byte.valueOf(valueOf)));
            strBuilder.append(" ");
        }
        return strBuilder.toString();
    }

    //-------------------------------------------------------
    //字节数组转转hex字符串，可选长度
    public static String ByteArrToHex(byte[] inBytArr, int offset, int byteCount) {
        StringBuilder strBuilder = new StringBuilder();
        int j = byteCount;
        for (int i = offset; i < j; i++) {
            strBuilder.append(Byte2Hex(Byte.valueOf(inBytArr[i])));
        }
        return strBuilder.toString();
    }

    // ascii byte数 组转成String字符串，可选长度
    public static String byteOfASCIIToString(byte[] inBytArr, int offset, int byteCount){
        StringBuilder strBuilder = new StringBuilder();
        int j = byteCount;
        for (int i = offset; i < j; i++) {
            char c=(char)inBytArr[i];
            strBuilder.append(c);
        }
        return strBuilder.toString();
    }


    //-------------------------------------------------------
    //转hex字符串转字节数组
    public static byte[] HexToByteArr(String inHex) {
        byte[] result;
        int hexlen = inHex.length();
        if (isOdd(hexlen) == 1) {
            hexlen++;
            result = new byte[(hexlen / 2)];
            inHex = "0" + inHex;
        } else {
            result = new byte[(hexlen / 2)];
        }
        int j = 0;
        for (int i = 0; i < hexlen; i += 2) {
            result[j] = HexToByte(inHex.substring(i, i + 2));
            j++;
        }
        return result;
    }

    /**
     * 按照指定长度切割字符串
     *
     * @param inputString 需要切割的源字符串
     * @param length      指定的长度
     * @return
     */
    public static List<String> getDivLines(String inputString, int length) {
        List<String> divList = new ArrayList<>();
        int remainder = (inputString.length()) % length;
        // 一共要分割成几段
        int number = (int) Math.floor((inputString.length()) / length);
        for (int index = 0; index < number; index++) {
            String childStr = inputString.substring(index * length, (index + 1) * length);
            divList.add(childStr);
        }
        if (remainder > 0) {
            String cStr = inputString.substring(number * length, inputString.length());
            divList.add(cStr);
        }
        return divList;
    }

    /**
     * 计算长度，两个字节长度
     *
     * @param val value
     * @return 结果
     */
    public static String twoByte(String val) {
        if (val.length() > 4) {
            val = val.substring(0, 4);
        } else {
            int l = 4 - val.length();
            for (int i = 0; i < l; i++) {
                val = "0" + val;
            }
        }
        return val;
    }

    /**
     * 校验和
     *
     * @param cmd 指令
     * @return 结果
     */
    public static String sum(String cmd) {
        List<String> cmdList = DataUtils.getDivLines(cmd, 2);
        int sumInt = 0;
        for (String c : cmdList) {
            sumInt += DataUtils.HexToInt(c);
        }
        String sum = DataUtils.IntToHex(sumInt);
        sum = DataUtils.twoByte(sum);
        cmd += sum;
        return cmd.toUpperCase();
    }

    public static int str8ToInt(String[] str){
        int sum=0;
        for(int i=0;i<str.length;i++){

            int value=computerBinary(str[i],str.length-1-i);

            sum+=value;
        }
        return sum;
    }
    public static int str8ToInt(String str){
        int sum=0;
        for(int i=0;i<str.length();i++){
            String strIndex=str.charAt(i)+"";
            int value=computerBinary(strIndex,str.length()-1-i);

            sum+=value;
        }
        return sum;
    }
    private static int computerBinary(String c, int index) {
        int vaule=Integer.valueOf(c);
        for(int i=0;i<index;i++){
            vaule*=2;
        }
        return vaule;
    }

}

```

## 参考
[GitHub地址](https://github.com/Deemonser/AndroidSerialPort)
[]()