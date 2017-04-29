---
title: 算法（java版）
date: 2017-03-30 21:43:22
tags: 冒泡 排序
---
## 冒泡排序
	
	public static void bubbleSort(int[] a) {
		if (a == null) {
			return;
		}

		for (int i = 0; i < a.length; i++) {
			for (int j = a.length - 1; j > i; j--) {
				if (a[j - 1] > a[j]) {
					int temp = a[j];
					a[j] = a[j - 1];
					a[j - 1] = temp;
				}

			}
		}

		for (int i = 0; i < a.length; i++) {
			System.out.println(a[i]);
		}
	}
	
## 朴素模式匹配算法

	//朴素模式匹配算法
		public static int index(String s,String t,int pos){
		int i=pos;//主串中第几个位置开始比较
		int j=0;//模式串中的第一个位置
		while(i<s.length()&&j<t.length()){
			if(s.charAt(i)==t.charAt(j)){
				i++;
				j++;
			}else{
				i=i-j+1;//主串的下一个位置
				j=0;//继续第一个位置
			}
		}
		if(j>=t.length()){
			return i-t.length();
		}else{
			return -1;
		}
	}
## KMP模式匹配算法	

	//////////KMP模式匹配算法////////////////
		public static int[] getNext(String t){
			System.out.println(t);
			int[] next=new int[t.length()];
			int i=1;
			int j=0;
			
			next[i]=j;
			
			while(i<t.length()-1){
				
				if(j==0||t.charAt(i)==t.charAt(j)){
	//				System.out.print("i="+i+" "+t.charAt(i)+"、");
	//				System.out.print("j="+j+" "+t.charAt(j)+";");
					i++;
					j++;
					next[i]=j;
				}else{
					System.out.println();
					j=next[j];//若字符不相等，则j值进行回溯。
				}
				
			}
			System.out.println();
			for(int k=0;k<next.length;k++){
				System.out.print(next[k]);
			}
			System.out.println();
			return next;
		}
		
		public static int indexKMP(String s,String t,int pos){
			int i=pos;//表示从主串中第几个字符串开始匹配
			int j=0;//表示从模式串中第几个字符串开始
			int[] next=getNext(t);
			while(i<s.length()&&j<t.length()){
				if(j==0||s.charAt(i)==t.charAt(j)){
					i++;
					j++;
				}else{
					j=next[j];
				}
			}
			if(j>=t.length()){
				return i-t.length();
			}else{
				return 0;
			}
			
		}
	