## LOCK类实现多线程打印

import java.util.concurrent.locks.Lock;  
import java.util.concurrent.locks.ReentrantLock;  
  
public class Main {  
    private Lock threadLock = new ReentrantLock();  
    private int flag = 0;  
    int count =10;  
  
    Thread aThread = new Thread(new Runnable() {  
        public void run() {  
            while (true) {  
                    // 锁定  
                    threadLock.lock();  
                    try {  
                        if ( count < 1) {  
                            return;  
                        }  
                        if (count%3 == 0 ) {  
                            // aThread的任务  
                            System.out.println("aThread --> " + count);  
                            count--;  
                        }  
                    } catch (Exception e) {  
                        // TODO: handle exception  
                    } finally {  
                        // 释放锁  
                        threadLock.unlock();  
                    }  
                }  
        }  
    });  
  
    Thread bThread = new Thread(new Runnable() {  
        public void run() {  
            while (true) {  
                    // 锁定  
                    threadLock.lock();  
                    try {  
                        if ( count < 1) {  
                            return;  
                        }  
                        if (count%3 == 1 ) {  
                            // aThread的任务  
                            System.out.println("bThread --> " + count);  
                            count--;  
                        }  
                    } catch (Exception e) {  
                        // TODO: handle exception  
                    } finally {  
                        // 释放锁  
                        threadLock.unlock();  
                }  
            }  
        }  
    });  
  
    Thread cThread = new Thread(new Runnable() {  
        public void run() {  
            while (true) {  
                    // 锁定  
                    threadLock.lock();  
                    try {  
                        if ( count < 1) {  
                            return;  
                        }  
                        if (count%3 == 2 ) {  
                            // aThread的任务  
                            System.out.println("cThread --> " + count);  
                            count--;  
                        }  
                    } catch (Exception e) {  
                        // TODO: handle exception  
                    } finally {  
                        // 释放锁  
                        threadLock.unlock();  
                }  
            }  
        }  
    });  
      
    public void startTwoThread() {  
        aThread.start();  
        bThread.start();  
        cThread.start();  
    }  
  
    public static void main(String[] args) {  
    	Main twoThreadPrinter = new Main();  
        twoThreadPrinter.startTwoThread();  
    }  
}  
