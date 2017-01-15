
开源地址：[https://github.com/open-android/JieCaoVideoPlayer](https://github.com/open-android/JieCaoVideoPlayer)

简书：[http://www.jianshu.com/p/618a5820261c](http://www.jianshu.com/p/618a5820261c "简书地址")
##主要特点

1. 视频全屏播放和浮层小窗播放
2. 可以完全自定义UI
3. 能在`ListView`、`ViewPager`和`ListView`、`ViewPager`和`Fragment`等多重嵌套模式下全屏工作
4. 手势修改进度和音量
5. 视频大小的屏幕适配，宽或长至少有两个对边是充满屏幕的，另外两个方向居中
6. 可以在加载、暂停、播放等各种状态中正常进入全屏和退出全屏
7. 重力感应自动全屏
8. WebView嵌套本地视频控件
9. [支持https和rtsp]{https://developer.android.com/guide/topics/media/media-formats.html}
10. 小于 100kb

##效果

**[jiecaovideoplayer-5.3.apk](https://github.com/lipangit/JieCaoVideoPlayer/releases/download/v5.3/jiecaovideoplayer-5.3.apk)**

![](http://upload-images.jianshu.io/upload_images/4037105-7a0137a2ca223736.gif?imageMogr2/auto-orient/strip)

## 使用步骤

### 1. 在project的build.gradle添加如下代码(如下图)

	allprojects {
	    repositories {
	        maven { url "https://jitpack.io" }
	    }
	}

![](http://upload-images.jianshu.io/upload_images/4037105-2faa5daca3bfe8a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	

### 2. 在Module的build.gradle添加依赖

      compile 'com.github.open-android:JieCaoVideoPlayer:0.1.0'



### 3.添加权限

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />

### 4.在Activity中添加如下代码

    listView = (ListView) findViewById(R.id.listview);
    adapterVideoList = new VideoListAdapter(this);
    listView.setAdapter(adapterVideoList);
    sensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
    sensorEventListener = new JCVideoPlayer.JCAutoFullscreenListener();

### 5.在Activity监听如下生命周期方法


    @Override
    public void onBackPressed() {
        if (JCVideoPlayer.backPress()) {
            return;
        }
        super.onBackPressed();
    }

    @Override
    protected void onResume() {
        super.onResume();
        Sensor accelerometerSensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        sensorManager.registerListener(sensorEventListener, accelerometerSensor, SensorManager.SENSOR_DELAY_NORMAL);
    }

    @Override
    protected void onPause() {
        super.onPause();
        sensorManager.unregisterListener(sensorEventListener);
        JCVideoPlayer.releaseAllVideos();
    }


### 6.新建一个Adapter继承BaseAdapter并且复制如下代码


  

    public static final String TAG = "JieCaoVideoPlayer";

    int[] videoIndexs = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    Context context;
    int pager = -1;

    public VideoListAdapter(Context context) {
        this.context = context;
    }

    public VideoListAdapter(Context context, int pager) {
        this.context = context;
        this.pager = pager;
    }

    @Override
    public int getCount() {
        return pager == -1 ? videoIndexs.length : 4;
    }

    @Override
    public Object getItem(int position) {
        return null;
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {

        ViewHolder viewHolder;
        if (null == convertView) {
            viewHolder = new ViewHolder();
            LayoutInflater mInflater = LayoutInflater.from(context);
            convertView = mInflater.inflate(R.layout.item_videoview, null);
            convertView.setTag(viewHolder);
        } else {
            viewHolder = (ViewHolder) convertView.getTag();
        }
        viewHolder.jcVideoPlayer = (JCVideoPlayerStandard) convertView.findViewById(R.id.videoplayer);
        if (pager == -1) {
            viewHolder.jcVideoPlayer.setUp(
                    VideoConstant.videoUrls[0][position], JCVideoPlayer.SCREEN_LAYOUT_LIST,
                    VideoConstant.videoTitles[0][position]);
            System.out.println("fdsfdsfdsfdsfa setup " + position);
            Picasso.with(convertView.getContext())
                    .load(VideoConstant.videoThumbs[0][position])
                    .into(viewHolder.jcVideoPlayer.thumbImageView);
        } else {
            viewHolder.jcVideoPlayer.setUp(
                    VideoConstant.videoUrls[pager][position], JCVideoPlayer.SCREEN_LAYOUT_LIST,
                    VideoConstant.videoTitles[pager][position]);
            Picasso.with(convertView.getContext())
                    .load(VideoConstant.videoThumbs[pager][position])
                    .into(viewHolder.jcVideoPlayer.thumbImageView);
        }
        return convertView;
    }

    class ViewHolder {
        JCVideoPlayerStandard jcVideoPlayer;
    }

### 7.添加Adapter的item布局R.layout.item_videoview

    <fm.jiecao.jcvideoplayer_lib.JCVideoPlayerStandard
        android:id="@+id/videoplayer"
        android:layout_width="match_parent"
        android:layout_height="200dp" />

### 8.在AndroidManifest.xml中

	<activity
	    android:name=".MainActivity"
	    android:configChanges="orientation|screenSize|keyboardHidden"
	    android:screenOrientation="portrait" /> <!-- or android:screenOrientation="landscape"-->


> 完成如上步骤就可以实现今日头条视频播放列表功能 , 更多功能请参考样例工程app

欢迎关注微信公众号

![](http://oi5nqn6ce.bkt.clouddn.com/itheima/booster/code/qrcode.png)

