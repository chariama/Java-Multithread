## A New Post
一个关于线程的经典面试题，要求用三个线程，按顺序打印1,2,3,4,5.... 71,72,73,74, 75.
线程1先打印1,2,3,4,5, 然后是线程2打印6,7,8,9,10, 然后是线程3打印11,12,13,14,15. 接着再由线程1打印16,17,18,19,20....以此类推, 直到线程3打印到75

public class Print1to75 {  
    static class Printer implements Runnable{  
        static int num = 1; //开始数字  
        static final int END = 75;  
        int id;  
        Object o; //这就是三个线程的“公证人”，有点寒酸吧  
          
        public Printer(int id, Object o) {  
            this.id = id;  
            this.o = o;  
        }  
          
        @Override  
        public void run(){  
            synchronized (o) {  
                while(num <= END){  
                    if(num / 5 % 3 == id){ //如果是属于自己的数，依次打印出来五个  
                        System.out.print(id + ":");  
                        for(int i = 0; i < 5; i++){  
                            System.out.print(num++ + ", ");  
                        }  
                        System.out.println();  
                          
                        o.notifyAll();//放弃CPU使用权，唤醒在o对象的等待队列上的线程  
                    }else{  
                        try {  
                            o.wait(); //如果不属于自己的数，把当前线程挂在o这个对象的等待队列上（也放弃了CPU使用权），等待唤醒  
                        } catch (InterruptedException e) {  
                            System.out.println("id" + "被打断了");  
                        }  
                    }  
                }  
            }  
        }  
    }  
      
      
    public static void main(String[] args) {  
        //下面可以不按0，1，2的顺序来,而且在两两中间随便sleep()，都会正确打印出来  
        Object o = new Object();  
        new Thread( new Printer(0, o)).start();  
        new Thread( new Printer(1, o)).start();  
        new Thread( new Printer(2, o)).start();  
    }  
}  


// way 2

import java.util.ArrayList;    
import java.util.List;    
import java.util.concurrent.ExecutorService;    
import java.util.concurrent.Executors;    
import java.util.concurrent.locks.Condition;    
import java.util.concurrent.locks.Lock;    
import java.util.concurrent.locks.ReentrantLock;    
    
public class Test {    
    private static final int TASK_NUM = 3;  
    private static int num = 0;    
    private static int flag = 0;    
    private static Lock lock = new ReentrantLock();    
    private static List<Condition> list = new ArrayList<Condition>();    
    private static ExecutorService exec = Executors.newCachedThreadPool();    
    static {    
        for(int i = 0; i < TASK_NUM; i++){  
            list.add(lock.newCondition());  
        }  
    }    
    
    private static void crit() {    
        if (num >= 75) {    
            System.exit(1);    
        }    
    }    
    
    private static void print() {    
        crit();    
        System.out.print(Thread.currentThread());    
        for (int i = 0; i < 5; i++) {    
            System.out.format("%-2d ", ++num);    
        }    
        System.out.println();    
    }    
    
    private static void work(int i) {    
            while (!Thread.interrupted()) {    
                try{    
                    lock.lock();    
                    if(flag == i){    
                        crit();    
                        print();    
                        flag = (i + 1) % list.size();    
                        list.get((i+1)%list.size()).signal();    
                    }else{    
                        try {    
                            list.get(i%list.size()).await();    
                        } catch (InterruptedException e) {    
                            e.printStackTrace();    
                        }    
                    }    
                }finally{    
                    lock.unlock();    
                }    
            }    
    }    
    
    private static class Task implements Runnable {    
        private final int i;    
    
        public Task(int i) {    
            this.i = i;    
        }    
    
        @Override    
        public void run() {    
            work(i);    
        }  
  
    }    
    
    /**  
     * @param args  
     */    
    public static void main(String[] args) {    
        for (int i = 0; i < list.size(); i++)    
            exec.execute(new Task(i));    
    }    
    
}
