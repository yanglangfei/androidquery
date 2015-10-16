Welcome to the androidquery GitHub home.

Click [here](http://code.google.com/p/android-query/wiki/AsyncAPI) for wiki at GoogleCode
or visit:
http://code.google.com/p/android-query/wiki/AsyncAPI

<<<<<<< HEAD
Click here to help out:
=======
>>>>>>> 7434e46bbf78506fa8f91518058cb26ed47597b8
<a href='https://pledgie.com/campaigns/22663'><img alt='Click here to lend your support to: AndroidQuery and make a donation at pledgie.com !' src='https://pledgie.com/campaigns/22663.png?skin_name=chrome' border='0' ></a>

Android之使用Android-query框架进行开发（一）

	开发Android使用Android-query框架能够快速的，比传统开发android所要编写的代码要少得很多，容易阅读等优势。 
        下载文档及其例子和包的地址：http://code.google.com/p/android-query/

	以下内容是我学习的一些心得分享：

第一节： 

复制代码
 	// 必须实现AQuery这个类
	AQuery aq = new AQuery(view);
	// 按顺序分析：取得xml对应控件id,设置图片,设置可以显示,点击事件（方法someMethod必须是public修饰）    

	aq.id(R.id.icon).image(R.drawable.icon).visible().clicked(this, "someMethod");  
	// 设置文字内容
        aq.id(R.id.name).text(content.getPname());
        aq.id(R.id.time).text(FormatUtility.relativeTime(System.currentTimeMillis(), content.getCreate())).visible();

	aq.id(R.id.desc).text(content.getDesc()).visible();  

复制代码
          

	 AQuery也支持Fragment：

复制代码
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        
        View view = inflater.inflate(getContainerView(), container, false);             
                
        aq = new AQuery(getActivity(), view);
        return view;
  }              
复制代码
     

第二节： 使用AQuery异步加载图片

2.1 从网上读取图片

	aq.id(R.id.image1).image(“图片URL”); 

 

2.2 缓存控制:  图片过大的话，避免记忆缓存

	boolean memCache = false;

     boolean fileCache = true;

     aq.id(R.id.image1).image("http://www.vikispot.com/z/images/vikispot/android-w.png", memCache, fileCache);
2.3 当下载太多图片的时候需要降低图片采样率，第四个参数为了保证图片质量，一般范围时200-399
   aq.id(R.id.image1).image(imageUrl, true, true, 200, 0);
2.4 如果下载图片失败,处理的方法：1. 设置一个预定的图片  2. 使imageview不可见或者是gone
     aq.id(R.id.image1).image(imageUrl, true, true, 0, R.drawable.default_image);
     aq.id(R.id.image1).image(imageUrl, true, true, 0, AQuery.INVISIBLE);
     aq.id(R.id.image1).image(imageUrl, true, true, 0, AQuery.GONE);

2.5 图片预加载
    // 从之前的url取得小图片
     String thumbnail = "http://farm6.static.flickr.com/5035/5802797131_a729dac808_s.jpg";   
     Bitmap preset = aq.getCachedImage(thumbnail);
    // 加载大图片前先显示小图片
     String imageUrl = "http://farm6.static.flickr.com/5035/5802797131_a729dac808_b.jpg";            
    aq.id(R.id.image).image(imageUrl, false, false, 0, 0, preset, AQuery.FADE_IN);
2.6 在加载图片的时候显示进度条,progress里面传入id
    String imageUrl = "http://farm6.static.flickr.com/5035/5802797131_a729dac808_b.jpg";            
     aq.id(R.id.image).progress(R.id.progress).image(imageUrl, false, false);
2.7 图片圆角显示,不支持大图片
     ImageOptions options = new ImageOptions();
     options.round = 15;
     aq.id(R.id.image).image(url, options);
2.8 图片长宽比例    
    // 保留原图片比例
    aq.id(R.id.image).image(imageUrl, true, true, 0, 0, null, AQuery.FADE_IN, AQuery.RATIO_PRESERVE);
    // 自定义图片比例
    //1:1, a square 
    aq.id(R.id.image2).image(imageUrl, true, true, 0, 0, null, 0, 1.0f / 1.0f);             
    aq.id(R.id.image3).image(imageUrl, true, true, 0, 0, null, 0, 1.5f / 1.0f);     
    //16:9, a video thumbnail
    aq.id(R.id.image4).image(imageUrl, true, true, 0, 0, null, 0, 9.0f / 16.0f);    
    aq.id(R.id.image5).image(imageUrl, true, true, 0, 0, null, 0, 3.0f / 4.0f);
2.9 图片描点，如果图片过高,描点可用来描述图片的哪一部分用于显示
    Anchor values:
1.0 : Display top of the image
0 : Display the center of the image
-1.0 : Display bottom of the image
AQuery.ANCHOR_DYNAMIC : Display image with a top bias for photos.
=======================================================
	ImageOptions options = new ImageOptions();
	options.ratio = 1;
	options.anchor = 1.0;
	aq.id(R.id.image1).image(imageUrl, options);
2.10 自定义图片加载后的处理
    aq.id(R.id.image1).image(imageUrl, true, true, 0, 0, new BitmapAjaxCallback(){}）;
2.11 异步从文件加载图片,建议使用降低采样率避免oom
    File file = new File(path);        
   //load image from file, down sample to target width of 300 pixels  
   aq.id(R.id.avatar).image(file, 300);
   //load image from file with callback
   aq.id(R.id.avatar).image(file, false, 300, new BitmapAjaxCallback(){
    @Override
    public void callback(String url, ImageView iv, Bitmap bm, AjaxStatus status){
        iv.setImageBitmap(bm);
	}
});
2.12 如果之前image("url")已经成功,之后的都可以直接使用而不需要重新访问网络,也就是说之后可以离线访问此图像资源
2.13 文件中获取缓冲图片
     File file = aq.getCachedFile(url);
2.14 除了imageview,webview也可以用来放图片
     aq.id(R.id.web).progress(R.id.progress).webImage(url);
2.15 延迟图片加载,帮助你是否加载正在快速滚动的listview,详情参考文档使用
2.16 图片不使用缓存
     aq.id(R.id.image).image(url, false, false);
2.17 缓存配置,缓存一般是保存在内部文件系统,但也可以保存在SDCard里面
      File ext = Environment.getExternalStorageDirectory();
      File cacheDir = new File(ext, "myapp"); 
      AQUtility.setCacheDir(cacheDir);
2.18 共享图片,为了与其他程序共享图片,你需要把文件放在SDCard,makeSharedFile方法创建缓存地址的一个副本
      File file = aq.makeSharedFile(url, "android.png");
         if(file != null){
                Intent intent = new Intent(Intent.ACTION_SEND);
                intent.setType("image/jpeg");
                intent.putExtra(Intent.EXTRA_STREAM, Uri.fromFile(file));
                startActivityForResult(Intent.createChooser(intent, "Share via:"), SEND_REQUEST);
        }
2.19 配置,最好把配置写在application的onCreate方法,详细参考文档
2.20 程序退出时候需要把缓存清除
      if(isTaskRoot()){
	  AQUtility.cleanCacheAsync(this);
	}
 或者：
       if(isTaskRoot()){
	//clean the file cache with advance option
        long triggerSize = 3000000; //大于3M时候开始清除
        long targetSize = 2000000;      //直到少于2M
        AQUtility.cleanCacheAsync(this, triggerSize, targetSize);
        }
2.21 低内存处理
      public class MainApplication extends Application{
          @Override
         public void onLowMemory(){  
        //clear all memory cached images when system is in low memory
        //note that you can configure the max image cache count, see CONFIGURATION
         BitmapAjaxCallback.clearCache();
	}
}
