# Used_car-API
## 0. Tech Stack💻

이 프로젝트는 Django와 djangorestframework를 사용했습니다.
> Django v3.1.3   
> Djangorestframework v3.12.2   
> Djangorestframework-jwt v1.11.0


## 1. Install🛠

프로젝트 레퍼지토리를 클론합니다.
```
git clone https://github.com/3661sy/Coding_test.git
```
프로젝트에 사용한 라이브러리를 pip를 이용해 다운받습니다.
```
pip install django
pip install djangorestframework
pip install djangorestframework-jwt
```
모두 설치가 끝나면 프로젝트가 있는 디렉토리로 이동해 서버를 실행합니다.

```
cd Coding_test
python .\manage.py runserver
```

## 2. Code📑
```python
###views.py
@api_view(["POST"])
@permission_classes((IsAuthenticated, ))
@authentication_classes((JSONWebTokenAuthentication, ))
def add_car(request):
    payload = json.loads(request.body)
    try:
        for data in payload['cars']:
            Car = car.objects.create(
                user=request.user,
                accident=data["accident"],
                repair=data["repair"],
                manufacturer=data["manufacturer"],
                price=data["price"]
            )
        return JsonResponse({'message': 'Success' }, safe=False, status=status.HTTP_201_CREATED)
    except ObjectDoesNotExist as e:
        return JsonResponse({'error': str(e)}, safe=False, status=status.HTTP_404_NOT_FOUND)
    except Exception:
        return JsonResponse({'error': 'Something is wrong'}, safe=False, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
```
`POST`로 들어온 JSON 객체를 `for in`을 사용하여 차례로 DB에 넣은 뒤 저장합니다. 성공적으로 저장되면 `{'message': 'Success' }`을 반환합니다.

```
@permission_classes((IsAuthenticated, ))
@authentication_classes((JSONWebTokenAuthentication, ))
```
`Decorator`을 사용해 사용자의 접근과 JWT를 가지고 있는지 확인합니다. 

```python
#setting.py

JWT_AUTH = {
    'JWT_SECRET_KEY': SECRET_KEY,
    'JWT_ALGORITHM': 'HS256',
    'JWT_ALLOW_REFRESH': True,
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=14),
}
```
`Access Token`의 만료시간은 하루로 지정했으며, `Refresh Token`같은 2주로 지정했습니다.
 
 > API 명세서: <https://documenter.getpostman.com/view/11976431/TVeqenc8>

## 3. 미구현 기능
### 3.1 다중 이미지 처리
서버에 Image와 JSON을 함께 보내야할 경우 대표적으로
> * `Multipart/form-data`로 보내는 방법
> * `Base64`로 인코딩하여 JSON 객체에 함께 보내는 방법
> * Image 파일과 JSON을 별도로 업로드하는 방법
 
 위 3가지의 방법 중, Multipart/form-data와 Base64로 인코딩하는 방법으로 고민하였습니다.    
  
 처음에는 `Base64`로 인코딩하는 방법을 먼저 생각하였으나, 인코딩과 디코딩에 대한 추가적 작업이 필요하였으며 이로인한 부하, 그리고 String이 길어질 경우 서버에서 읽지 못하는 경우가 있었습니다. 따라서 다른 방법을 물색해, `form-data` 로 보내는 방식을 고려하게 되었습니다.

## 4. Question🙋‍♀️
### 4.1 서버에 변동사항이 있을 경우 프론트에서 Redirect 신호를 줄 것인가?
예시로 사용자가 두 개의 창을 동시에 키고 데이터를 입력하였을 때, A라는 창에서 임시저장을 눌러 서버에는 A라는 데이터가 임시저장되었습니다. 하지만 B라는 창에 있던 기존의 데이터를 임시저장된 A의 데이터로 변동할 것인지, 기존 데이터를 유지할 것인지 궁금합니다.

### 4.2 회사에서는 django를 이용할 때 `FBV(Function Base View)`와 `CBV(Class Base View)` 중 어떤 방식으로 짜는 지
제가 개발한 코드같은 경우 함수기반 View로 코드를 짰습니다. 그러면서 회사에서, 즉 현업에서는 FBV와 CBV 중 어떤 것을 기반으로 코드를 짜는 지 궁금합니다.

### 4.3 사용하는 DB가 무엇인지
보내주신 Notion과 사이트에도 DB는 무엇을 쓰는지 나오지 않아 개인적으로 궁금하여 여쭤보고 싶습니다.
