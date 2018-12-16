package com.robotleo.hardware.sensor.model;

import android.os.Handler;
import android.os.Looper;

/**
 * Created by leorobot on 2018/1/4.
 */

public class WheelMotorHelper {

    public final static int SPEED_MODE = 0;// 速度模式
    public final static int LOCATION_MODE = 1;// 位置模式
    public final static float WHEEL_SIZE_DEFAULT = 17;// 直径17cm=6.5寸
    public final static float WHEEL_SIZE_EIGHT = 20;// 直径20cm=8寸
    public final static float WHEEL_SIZE_NNINE = 24.5f;// 直径24.5cm=9寸
    public final static float WHEEL_SIZE_TEN = 26;// 直径26cm=10寸

    private static WheelMotorHelper wheelMotorHelper = null;
    private static final int ROTATE_DISTANCE_UNIT = 28; //旋转1度的距离
//    private static final float ROTATE_SPEED_UNIT = 0.0000608479035f; //旋转1度的速度差（m/minute）
    private static final float ROTATE_SPEED_UNIT = 0.00365087421f; //旋转1度的速度差（m/s）
    private volatile boolean isQuery = false;// 是否查询
    private int trunAngle = 0;
    private volatile boolean isStart = false;// 是否已经启动
    private int model = -1;// 默认为速度模式   0:速度模式   1:位置模式
    private int location_sub = 0;// 默认为相对位置  0:相对位置   1:绝对位置
    private Handler handler = new Handler(Looper.getMainLooper());
    private float currentSize = WHEEL_SIZE_DEFAULT;// 当前轮子大小
    private final float PAI = 3.1415926f;// 圆周率π
    public final static int  WHEEL_TURN_RADIAN_FORWARD = 1;
    public final static int  WHEEL_TURN_RADIAN_BACK = 2;
    private int mTrunRadianBigSpeed = 0;
    private int mTrunRadianSmallSpeed = 0;

    public static WheelMotorHelper getInstance(){
        if (null == wheelMotorHelper){
            synchronized (WheelMotorHelper.class){
                if (null == wheelMotorHelper){
                    wheelMotorHelper = new WheelMotorHelper();
                }
            }
        }
        return wheelMotorHelper;
    }

    private WheelMotorHelper(){
    }

    /**
     * 设定轮子大小有6.5寸，8寸，9寸，10寸
     * @param currentSize
     */
    public void setCurrentSize(float currentSize) {
        this.currentSize = currentSize;
    }

    public void start(){
        isStart = true;
        WheelMotorControlUtil.start(WheelMotorControlUtil.ALL_WHEEL);
    }

    /**
     * 前进
     * @param speed_location_i  速度(m/s)或者位置(m)
     */
    public void forward(float speed_location_i){
        if (1 == model){
            if (!isStart){
                start();
            }
            final long location = locationConversion(speed_location_i);
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.LEFT_FRONT);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.RIGHT_AFTER);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.LEFT_AFTER);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.RIGHT_FRONT);
                }
            }, 10);
        }else {
            if (!isStart){
                handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        start();
                    }
                }, 5);
            }
            final int speed = speedConersion(speed_location_i);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.LEFT_FRONT);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.RIGHT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.LEFT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.RIGHT_FRONT);
        }
    }
    /**
     * 后退
     * @param speed_location_i 速度或者位置
     */
    public void backOff(float speed_location_i){
        if (1 == model){
            if (!isStart){
                start();
            }
            final long location = locationConversion(speed_location_i);
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.LEFT_FRONT);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.RIGHT_AFTER);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.LEFT_AFTER);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.RIGHT_FRONT);
                }
            }, 10);
        }else {
            if (!isStart){
                handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        start();
                    }
                }, 5);
            }
            final int speed = speedConersion(speed_location_i);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.LEFT_FRONT);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.RIGHT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.LEFT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.RIGHT_FRONT);
        }
    }
    /**
     * 原地左转
     * @param speed_location_i 速度或者位置
     */
    public void turnLeft(float speed_location_i){
        if (1 == model){
            if (!isStart){
                start();
            }
            final long location = locationConversion(speed_location_i);
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.LEFT_FRONT);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.RIGHT_AFTER);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.LEFT_AFTER);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.RIGHT_FRONT);
                }
            }, 10);
        }else {
            if (!isStart){
                handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        start();
                    }
                }, 5);
            }
            final int speed = speedConersion(speed_location_i);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.LEFT_FRONT);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.RIGHT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.LEFT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.RIGHT_FRONT);
        }
    }
    /**
     * 原地右转
     * @param speed_location_i 速度或者位置
     */
    public void turnRight(float speed_location_i){
        if (1 == model){
            if (!isStart){
                start();
            }
            final long location = locationConversion(speed_location_i);
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.LEFT_FRONT);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.RIGHT_AFTER);
                    WheelMotorControlUtil.locationControl(location, WheelMotorControlUtil.LEFT_AFTER);
                    WheelMotorControlUtil.locationControl(-location, WheelMotorControlUtil.RIGHT_FRONT);
                }
            }, 10);
        }else {
            if (!isStart){
                handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        start();
                    }
                }, 5);
            }
            final int speed = speedConersion(speed_location_i);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.LEFT_FRONT);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.RIGHT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(speed, WheelMotorControlUtil.LEFT_AFTER);
            WheelMotorControlUtil.speedModelSpeedControl(-speed, WheelMotorControlUtil.RIGHT_FRONT);
        }
    }
    /**
     * 原地旋转
     * @param angle 旋转角度  正数表示顺时针旋转(右转)，负数表示逆时针旋转
     */
    public void wheelTurn(int angle){
        if (!isStart){
            start();
        }
        if(Math.abs(angle) > 360){
            trunAngle = (angle%360) * ROTATE_DISTANCE_UNIT;
        }else{
            trunAngle = angle * ROTATE_DISTANCE_UNIT;
        }
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                WheelMotorControlUtil.locationControl(trunAngle, WheelMotorControlUtil.LEFT_FRONT);
                WheelMotorControlUtil.locationControl(-trunAngle, WheelMotorControlUtil.RIGHT_AFTER);
                WheelMotorControlUtil.locationControl(trunAngle, WheelMotorControlUtil.LEFT_AFTER);
                WheelMotorControlUtil.locationControl(-trunAngle, WheelMotorControlUtil.RIGHT_FRONT);
            }
        }, 10);
    }
    /**
     * 弧度转弯
     * @param angle 弧度转弯角度（0-90）  正数表示右弧度转弯，负数表示左弧度转弯
     * @param type 前进还是后退
     */
    public void wheelTurnRadian(final int angle,final int type){
        if (!isStart){
            start();
        }
        modelSetting(SPEED_MODE);
        if(angle > 90 || angle < -90){
            mTrunRadianBigSpeed = speedConersion(0.2f);
            mTrunRadianSmallSpeed = 0;
        }else{
            mTrunRadianBigSpeed = speedConersion(0.2f);
            mTrunRadianSmallSpeed = speedConersion((90-Math.abs(angle))/90.f*0.2f);
        }
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                if(type == WHEEL_TURN_RADIAN_FORWARD){
                    if(angle > 0){
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianBigSpeed, WheelMotorControlUtil.LEFT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianBigSpeed, WheelMotorControlUtil.LEFT_AFTER);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianSmallSpeed, WheelMotorControlUtil.RIGHT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianSmallSpeed, WheelMotorControlUtil.RIGHT_AFTER);
                    }else{
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianBigSpeed, WheelMotorControlUtil.RIGHT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianBigSpeed, WheelMotorControlUtil.RIGHT_AFTER);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianSmallSpeed, WheelMotorControlUtil.LEFT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(mTrunRadianSmallSpeed, WheelMotorControlUtil.LEFT_AFTER);
                    }
                }else if(type == WHEEL_TURN_RADIAN_BACK){
                    if(angle > 0){
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianBigSpeed, WheelMotorControlUtil.LEFT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianBigSpeed, WheelMotorControlUtil.LEFT_AFTER);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianSmallSpeed, WheelMotorControlUtil.RIGHT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianSmallSpeed, WheelMotorControlUtil.RIGHT_AFTER);
                    }else{
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianBigSpeed, WheelMotorControlUtil.RIGHT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianBigSpeed, WheelMotorControlUtil.RIGHT_AFTER);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianSmallSpeed, WheelMotorControlUtil.LEFT_FRONT);
                        WheelMotorControlUtil.speedModelSpeedControl(-mTrunRadianSmallSpeed, WheelMotorControlUtil.LEFT_AFTER);
                    }
                }
            }
        }, 10);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                statusQuery();
            }
        }, 20);
    }

    /**
     * 刹车
     */
    public void brakes(){
        isQuery = false;
        if (1 == model){
            stop();
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    start();
                }
            }, 10);
        }else {
            WheelMotorControlUtil.speedModelBrakes(WheelMotorControlUtil.ALL_WHEEL);
        }
    }

    /**
     * 释放电机
     */
    public void stop(){
        isStart = false;
        WheelMotorControlUtil.stop(WheelMotorControlUtil.ALL_WHEEL);
    }

    /**
     * 速度模式下加速度设定
     * @param acc_speed_i 加减速度
     */
    public void accDecSpeedSetting(int acc_speed_i){
        if (0 == model){
            WheelMotorControlUtil.accDecSpeed(acc_speed_i, acc_speed_i, WheelMotorControlUtil.ALL_WHEEL);
        }
    }

    /**
     * 清除故障
     */
    public void clearFault(){
        isStart = false;
        WheelMotorControlUtil.clearFault(WheelMotorControlUtil.ALL_WHEEL);
    }

    /**
     * 速度/位置模式设定
     * @param type 0：速度模式，1：位置模式
     */
    public void modelSetting(int type){
        if (1 == type){
            if(model != 1){
                model = 1;
                WheelMotorControlUtil.modelSetting(1, WheelMotorControlUtil.ALL_WHEEL);
            }
        }else {
            if(model != 0){
                model = 0;
                WheelMotorControlUtil.modelSetting(0, WheelMotorControlUtil.ALL_WHEEL);
            }
        }
    }

    /**
     * 设定相对位置/绝对位置
     * @param locationModel 0:相对位置，1:绝对位置
     */
    public void locationSubControl(int locationModel){
        if (1 != model){
            return;
        }
        if (1 == locationModel){
            location_sub = 1;
            WheelMotorControlUtil.locationSubControl(1, WheelMotorControlUtil.ALL_WHEEL);
        }else {
            location_sub = 0;
            WheelMotorControlUtil.locationSubControl(0, WheelMotorControlUtil.ALL_WHEEL);
        }
    }

    /**
     * 位置模式下速度设定
     * @param speed 设定的速度单位是RPM（转/分钟）
     */
    public void locationModelSpeedSetting(int speed){
        WheelMotorControlUtil.locationModelSpeedSetting(speed, WheelMotorControlUtil.ALL_WHEEL);
    }

    /**
     * 轮电机状态查询模块
     */
    public void statusQuery(){
        WheelMotorControlUtil.statusQuery(WheelMotorControlUtil.ALL_WHEEL);
    }

    /**
     * 把m/s转换成RPM
     * @param speed
     * @return
     */
    private int speedConersion(float speed){
        int rpm = (int) Math.round((speed*60*100)/(PAI*currentSize));
        return rpm;
    }

    /**
     * 把m转换成位置
     * 一圈=4096个位置
     * @param location
     * @return
     */
    private long locationConversion(float location){
        return Math.round((location*100*4096)/(PAI*currentSize));
    }

    /**
     * 前进
     */
    public void goForward(final float speed){
        modelSetting(SPEED_MODE);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                forward(speed);
            }
        }, 10);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                statusQuery();
            }
        }, 20);
    }

    /**
     * 后退
     */
    public void goBackoff(final float speed){
        modelSetting(SPEED_MODE);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                backOff(speed);
            }
        }, 10);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                statusQuery();
            }
        }, 20);
    }

    /**
     * 左转
     */
    public void goTurnLeft(final int angle){
        modelSetting(LOCATION_MODE);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                wheelTurn(-angle);
            }
        }, 10);
    }

    /**
     * 右转
     */
    public void goTurnRight(final int angle){
        modelSetting(LOCATION_MODE);
        handler.postDelayed(new Runnable() {
            @Override
            public void run() {
                wheelTurn(angle);
            }
        }, 10);
    }

    /**
     * int jiaodu, final float speed_location_i
     * @param jiaodu
//     * @param speed_location_i
     */
    public void aaaaaLeft(int jiaodu){
        isQuery = true;
        if (!isStart){
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    start();
                }
            }, 5);
        }
        final int speedL = speedConersion(0.15f);
        final int speedR = speedConersion(0.15f+(jiaodu*ROTATE_SPEED_UNIT));
        WheelMotorControlUtil.speedModelSpeedControl(speedL, WheelMotorControlUtil.LEFT_FRONT);
        WheelMotorControlUtil.speedModelSpeedControl(speedR, WheelMotorControlUtil.RIGHT_AFTER);
        WheelMotorControlUtil.speedModelSpeedControl(speedL, WheelMotorControlUtil.LEFT_AFTER);
        WheelMotorControlUtil.speedModelSpeedControl(speedR, WheelMotorControlUtil.RIGHT_FRONT);
        new Thread(){
            @Override
            public void run() {
                while (isQuery){
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    statusQuery();
                }
            }
        }.start();
    }

    /**
     * int jiaodu, final float speed_location_i
     * @param jiaodu
//     * @param speed_location_i
     */
    public void aaaaaRight(int jiaodu){
        isQuery = true;
        if (!isStart){
            handler.postDelayed(new Runnable() {
                @Override
                public void run() {
                    start();
                }
            }, 5);
        }
        final int speedL = speedConersion(0.15f+(jiaodu*ROTATE_SPEED_UNIT));
        final int speedR = speedConersion(0.15f);
        WheelMotorControlUtil.speedModelSpeedControl(speedL, WheelMotorControlUtil.LEFT_FRONT);
        WheelMotorControlUtil.speedModelSpeedControl(speedR, WheelMotorControlUtil.RIGHT_AFTER);
        WheelMotorControlUtil.speedModelSpeedControl(speedL, WheelMotorControlUtil.LEFT_AFTER);
        WheelMotorControlUtil.speedModelSpeedControl(speedR, WheelMotorControlUtil.RIGHT_FRONT);

        new Thread(){
            @Override
            public void run() {
                while (isQuery){
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    statusQuery();
                }
            }
        }.start();
    }
}
