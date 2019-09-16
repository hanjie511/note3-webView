# note3-webView
# 笔记3：Android中webView的使用
```java
public class DzxTimeLineFragment extends Fragment {
	WebView webView;
	ProgressBar progressBar;
	String url="";
	public final static String WEBERR_NETWORK="file:///android_asset/www/networkerr.html";
	public DzxTimeLineFragment(String url) {
		this.url=url;
	}
	@Override
	public void onActivityCreated(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onActivityCreated(savedInstanceState);
		initWebView();
		loaddingURL(url);
	}
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		View view=inflater.inflate(com.yys.bdsz.R.layout.dzx_timeline_fragment, container,false);
		webView=(WebView) view.findViewById(com.yys.bdsz.R.id.webView_dzxTimeLine);
		progressBar=(ProgressBar) view.findViewById(com.yys.bdsz.R.id.progress_bar_dzxTimeLine);
		return view;
	}
	private void initWebView() {
	
		webView.getSettings().setJavaScriptEnabled(true);

		webView.setWebChromeClient(new ChromeClient());
		
		webView.getSettings().setUseWideViewPort(true);
		
		webView.getSettings().setBuiltInZoomControls(true);
		
		webView.addJavascriptInterface(getHtmlObject(), "Android");
		webView.setWebViewClient(new WebViewClient() {

			@Override
			public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {
				// TODO Auto-generated method stub
				super.onReceivedError(view, errorCode, description, failingUrl);
				webView.loadUrl(WEBERR_NETWORK);
			}
			
		});
		
	}

	private Object getHtmlObject() {
		Object object=new Object() {
			@JavascriptInterface
			public void showProgressDetail(String id) {
				Intent intent=new Intent();
				intent.setClass(getActivity(), DzxProgressActivity.class);
				String url=UrlUtil.MAIN_SERVER+"getDZXProgressDetail.html?id="+id+"";
				intent.putExtra("url", url);
				startActivity(intent);
			}
		};
		return object;
	}
	//Set progressBar'progress
	class ChromeClient extends WebChromeClient {

		@Override
		public void onProgressChanged(WebView view, int newProgress) {
			// TODO Auto-generated method stub
			super.onProgressChanged(view, newProgress);
			if (newProgress >= 100) {
				progressBar.setVisibility(View.GONE);
			} else {
				progressBar.setProgress(newProgress);
			}
		}

	}
	//setting the method of loading webView
	public void loaddingURL(String url) {
		try {
			if (webView != null) {
				webView.requestFocus();
				webView.loadUrl(url);
			}
		} catch (Exception e) {
			webView.loadUrl(WEBERR_NETWORK);
			return;
		}
	}
	@Override
	public void onDestroy() {
		// TODO Auto-generated method stub
		super.onDestroyView();
		webView.clearCache(true);
		webView.destroy();
	}
	@Override
	public void onConfigurationChanged(Configuration newConfig) {
		// TODO Auto-generated method stub
		super.onConfigurationChanged(newConfig);
	}
}
