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
	