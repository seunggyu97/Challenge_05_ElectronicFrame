# Challenge_05
## 전자액자

사용자의 기기에서 이미지를 불러온 뒤 애니메이션을 통해 사진을 전환한다.

## 기능

* Android 권한 허용 ("사진 추가하기" 버튼 클릭 시 권한 요구)
* 권한 설명 팝업 (권한 거절 후 "사진 추가하기" 버튼 재클릭 시 교육용 UI (접근권한 설명 팝업) 출력
* 사용자 기기에 있는 사진을 Layout에 출력 (최대 9장)
* "전자액자 실행하기" 버튼 클릭 시 추가된 사진을 순서대로 애니메이션을 이용해 전환 (가로모드)

## 학습 내용

* Layout
  * 가로 화면으로 전환
* Android Permission
  * **Manifest**
  * ``` kotlin
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    ```
* 이미지 파일 가져오기
  * **MainActivity**
  * ``` kotlin
    addPhotoButton.setOnClickListener {
            when{
                ContextCompat.checkSelfPermission(
                // 접근 권한 체크
                    this,
                    android.Manifest.permission.READ_EXTERNAL_STORAGE
                ) == PackageManager.PERMISSION_GRANTED ->{
                    // 권한이 있는 경우
                    navigatePhotos()
                }
                shouldShowRequestPermissionRationale(android.Manifest.permission.READ_EXTERNAL_STORAGE) ->{
                    // 권한이 없는 경우, 교육용 팝업 확인 후 권한 팝업을 띄우는 기능
                    showPermissionContextPopup()
                }
                else -> {
                    // 위 두개의 상태가 아닐경우(버튼 클릭이 처음일 경우)
                    requestPermissions(arrayOf(android.Manifest.permission.READ_EXTERNAL_STORAGE),1000)
                }
            }
        }
     ```
     
     ``` kotlin
     override fun onRequestPermissionsResult(
         requestCode: Int,
         permissions: Array<out String>,
         grantResults: IntArray
     ) {
         super.onRequestPermissionsResult(requestCode, permissions, grantResults)

         when(requestCode){
             1000 -> {
                 if(grantResults.isNotEmpty() && grantResults[0] == PackageManager.PERMISSION_GRANTED){
                     navigatePhotos()
                 } else{
                     Toast.makeText(this,"권한을 거부하셨습니다.", Toast.LENGTH_SHORT).show()
                 }
             }
             else -> {

             }
         }
     }
     ```

    ``` kotlin
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        if(resultCode != Activity.RESULT_OK){
            return
        }

        when(requestCode){
            2000 -> {
                val selectedImageUri: Uri? = data?.data
                if(selectedImageUri != null){

                    if(imageUriList.size == 9) {
                        Toast.makeText(this, "이미 사진이 꽉 찼습니다.",Toast.LENGTH_SHORT).show()
                        return
                    }

                    imageUriList.add(selectedImageUri)
                    imageViewList[imageUriList.size - 1].setImageURI(selectedImageUri)

                }else {
                    Toast.makeText(this, "사진을 가져오지 못했습니다.",Toast.LENGTH_SHORT).show()
                }

            }
            else -> {
                Toast.makeText(this, "사진을 가져오지 못했습니다.", Toast.LENGTH_SHORT).show()
            }
        }
    }
* View Animation
* Content Provider
  * SAF (Storage Access Framework)
  * <https://developer.android.com/guide/topics/providers/document-provider?hl=ko>
     ``` kotlin
     private fun navigatePhotos(){
        val intent = Intent(Intent.ACTION_GET_CONTENT)
        intent.type = "image/*"
        startActivityForResult(intent, 2000)
    }
    ```
* Activity Lifecycle
  * <https://developer.android.com/guide/components/activities/activity-lifecycle?hl=ko>
![activity_lifecycle](https://user-images.githubusercontent.com/74666576/151051712-98e4576e-ebc7-4982-8d82-5400970118de.jpg)

# 실행화면

<img src="https://user-images.githubusercontent.com/74666576/151050195-ddcd60b4-070e-4905-9ac6-9157e252a32f.jpg" width="270" height="500"><img src="https://user-images.githubusercontent.com/74666576/151050182-735331a9-ae69-422a-a2e7-7510e162f934.jpg" width="270" height="500"><img src="https://user-images.githubusercontent.com/74666576/151050148-555c96ac-0065-463e-ae75-8bb34e8c589b.jpg" width="270" height="500"><img src="https://user-images.githubusercontent.com/74666576/151050156-7c9b8c51-46c0-4402-ae03-f70cf2fcadd4.jpg" width="270" height="500"><img src="https://user-images.githubusercontent.com/74666576/151050131-fd85a9c1-aec0-4a43-9eb9-d5a585fe9846.jpg" width="270" height="500">
![SmartSelect_20220126-045538](https://user-images.githubusercontent.com/74666576/151050120-83fac4a5-3805-491b-af00-f47d64e8be07.gif)
