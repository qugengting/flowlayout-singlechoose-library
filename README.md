# flowlayout-singlechoose-library
android流式布局单选（带输入框可增删）库
先看效果图，这个贴不出动态图，请看我的博客：https://blog.csdn.net/qugengting/article/details/81324030

    应该很明了了，流式布局，前面可以加个标题（也支持去掉），最后面默认带个输入框（也可以设置不带），布局主要内容是各个标签，单击选中，
会自动弹出软键盘，按删除键就把选中的标签删掉；也支持通过软键盘的删除键自动选中最后一个标签，再按删除键就把该标签删除；支持输入框输入
以新增标签，当输入内容并加上逗号，会自动识别并把逗号前面的内容作为新增的标签加入到布局当中。可以看到，发邮件选收件人时这个布局还是很
好用的。右侧的加号按钮可以批量新增标签，在写邮件时就可以点击跳转到通讯录列表界面选择多个收件人了，再把数据带回这个布局当中作为新增的
标签。

    该布局是参照张鸿洋的相关博客写成的，核心是流式布局和标签的单选，巧妙的地方是给每个流式布局的子控件加上一层FrameLayout便于统一
处理，标题、输入框和选中删除是我新增的。具体实现就不多说了，张鸿洋的博客写得非常清楚：https://blog.csdn.net/lmj623565791/article/details/38352503
https://blog.csdn.net/lmj623565791/article/details/48393217

    这里直接看怎么用这个布局吧：

添加仓库：在项目根目录的build.gradle加上jitpack仓库：

allprojects {

    repositories {

        ...

        maven { url 'https://jitpack.io' }

    }

}

添加依赖：在项目的build.gradle当中添加依赖：

dependencies {

    implementation 'com.github.qugengting:flowlayout-singlechoose-library:1.0.0'

}

界面调用：

public class MainActivity extends Activity {
    private String[] mVals = new String[]{"a@qq.com", "b@qq.com", "c@126.com", "d@hotmail.com", "e@foxmail.com"};
    private LayoutInflater mInflater;
    private ImageButton mImageButtonAdd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_main);
        mInflater = LayoutInflater.from(this);
        TagFlowLayout flowLayout = findViewById(R.id.id_flowlayout);
        flowLayout.setAttachLabel(true);//设置是否需要添加标签,默认添加
        flowLayout.setAdapter(adapter);
        mImageButtonAdd = findViewById(R.id.ib_add);
        mImageButtonAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                adapter.add("addI@sina.com");
                adapter.add("addII@sina.com");
                adapter.add("addIII@sina.com");
                adapter.notifyDataChanged();
            }
        });
    }

    private TagAdapter adapter = new TagAdapter(mVals) {
        @Override
        public View getView(FlowLayout parent, int position, String s) {
            TextView tv = (TextView) mInflater.inflate(R.layout.tv_item, parent, false);
            tv.setText(s);
            return tv;
        }

        @Override
        public View getLabelView(FlowLayout parent) {
            //如果设置flowLayout.setAttachLabel(false);该标签将不显示
            TextView tv = (TextView) mInflater.inflate(R.layout.tv_label, parent, false);
            tv.setText("收件人：");
            return tv;
        }

        @Override
        public View getInputView(FlowLayout parent) {
            return mInflater.inflate(R.layout.edt, parent, false);
        }
    };

}

很简单吧，adapter实现三个方法，分别是标签、标题和输入框的布局，然后flowlayout设置setAdapter就可以了，如果不用标题，
那么就设置setAttachLabel(false)，至于数据的增删，可以操作adapter并adapter.notifyDataChanged()就可以了。
