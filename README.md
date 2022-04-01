# Retrofit2
다시 학습하는 Retrofit2

option 1 : a resource relative to your base URL
----------------------------------------------------------------------
@GET("baseUrl")

Call<ResponseBody> downloadFileWithFixedUrl();
  
option 2 :  using a dynamic URL
----------------------------------------------------------------------
@GET

Call<ResponseBody> downloadFileWithDyamicUrlSync(@Url String fileUrl)  

다운로드하려는 파일이 정적 리소스(서버의 동일한 위치에 존재)인 경우 Base URL은 해당 파일이 있는
서버를 가리키며 이 경우 옵션 1을 사용하도록 선택할 수 있다. 
  
두 번째 옵션은 Retrofit2의 새로운 기능이다. 이제 전체 경로 요청으로 동적 주소를 
쉽게 구성할 수 있다. 특수 파일의 다운로드에 매우 유용할 수 있다. 
즉, 요청이 사용자 정보 또는 타임스탬프와 같은 일부 매개변수에 따라 달라질 수 있다.   
  
  
요청 후 호출 하는 법 
---------------------------------------------------------------------------------------------
FileDownloadService downloadService = ServiceGenerator.create(FileDownloadService.class);

Call<ResponseBody> call = downloadService.downloadFileWithDynamicUrlSync(fileUrl);
  
call.enqueue(new Callback<ResponseBody>() {
  
  @Override
  
  public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
    if(response.isSuccess()){
      boolean writtenToDisk = writeResponseBodyToDisk(response.body());
      }else{
         Log.d(TAG, "실 패 :(")
      }
  }
 
  @Override
  
  public void onFailure(Call<ResponseBody> call, Throwable t) {
    Log.d(TAG,"error");
  }
  
 });
  
  
  
  
  
  
 파일을 저장하는 방법
 ------------------------------------------------------
 
 writeResponseBodyToDisk() 메서드는 ResponseBody 바이트를 읽고 디스크에 쓰는 방식으로 
 개체를 보유한다. 
  
 private boolean writeResponseBodyToDisk(ResponseBody body) {  
  
    try {
        File futureStudioIconFile = new File(getExternalFilesDir(null) + File.separator + "Future Studio Icon.png");

        InputStream inputStream = null;
  
        OutputStream outputStream = null;

        try {
  
            byte[] fileReader = new byte[4096];

            long fileSize = body.contentLength();
  
            long fileSizeDownloaded = 0;

            inputStream = body.byteStream();
  
            outputStream = new FileOutputStream(futureStudioIconFile);

            while (true) {
                int read = inputStream.read(fileReader);

                if (read == -1) {
  
                    break;
                }

                outputStream.write(fileReader, 0, read);

                fileSizeDownloaded += read;

                Log.d(TAG, "file download: " + fileSizeDownloaded + " of " + fileSize);
            }

            outputStream.flush();

            return true;
  
        } catch (IOException e) {
  
            return false;
  
        } finally {
  
            if (inputStream != null) {
  
                inputStream.close();
            }

            if (outputStream != null) {
  
                outputStream.close();
            }
        }
  
    } catch (IOException e) {
  
        return false;
    }
  
}
