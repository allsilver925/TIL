## AWS S3

### S3 버킷에 로컬 파일을 올리려고 할 때, aws-cli로 접근하여 업로드/다운로드가 가능함.

1. aws-cli 다운로드
```pip install awscli```

2. Configure로 S3에 접근 (해당 버킷에 맞는 정보 입력) <br>
```
   >> aws configure
   >> AWS Access Key ID :
   >> AWS Secret Access Key :
   >> Default region name : 
   >> Default output format :
```
<br>

3. 업로드 <br>

``` aws s3 cp Filename.csv s3://bucketName/Filename.csv ```

4. 해당 디렉토리의 모든 파일 업로드 <br>

``` aws s3 cp ./ s3://bucketName/ --recursive```
