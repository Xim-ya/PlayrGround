###  ✨MVVM BASED SIMPLE PROJECT ✨

## 목차
- [개발 동기](#개발-동기)
- [요약](#요약)
- [학습한 내용](#학습한-내용)

## 개발 동기
- <a href="https://cs193p.sites.stanford.edu/?gclid=Cj0KCQiA9OiPBhCOARIsAI0y71AAz_kpQcilmi8FF8nX9eQG4YadS2FCacNxbzbtKSsUkr9ZQzcf1QQaAnHUEALw_wcB">Stanford CS193p for IOS </a> 강의를 듣고 ``MVVM`` 아키텍쳐와 관련된 내용 학습
- Flutter 프레임워크를 사용하여 Swift에서 주로 사용되는 ``MVVVM `` 패턴을 적용하는 프로젝트를 진행하여 관련 아키텍쳐의 이해도를 높이려 함.
- <a href="https://cs193p.sites.stanford.edu/?gclid=Cj0KCQiA9OiPBhCOARIsAI0y71AAz_kpQcilmi8FF8nX9eQG4YadS2FCacNxbzbtKSsUkr9ZQzcf1QQaAnHUEALw_wcB">Stanford CS193p for IOS </a> &  <a href ="https://www.youtube.com/watch?v=7n2QprcdHMA">유튜브 강의</a>를 기반으로 프로젝트를 진행함(전체적인 구조는 다름) 

## 요약

| Index          | Detail                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 기여             | 개인 프로젝트 (개발 / 디자인 )                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 시연 연상          | https://youtu.be/NZ-elImbVLY                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 구현 영상          | https://youtu.be/NZ-elImbVLY                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 구현 기간          | 2022.02.16 (하루)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 구현 목표          | - MVVM 패턴을 적용 <br> - 어플리케이션에서 사용되는 메인 비즈니스 로직과 데이터들을 Model에서 관리 <br> - ``News API``를 사용하여 Json 데이터를 받고 화면에 보여줌<br/> - 뉴스 즐겨찾기 기능 (추가, 삭제)<br/> - 각 국가를 기준으로 헤드라인 뉴스를 호출하여 화면에 보여줌(옵션 선택 시) <br> - API 호출하고 화면에 보여줄 때 네트워크 예외 처리 진행                                                                                                                                                                                                                                                                                                                         | 
| 구현 기능          | **[홈]** <br> - API(헤드라인 뉴스 리스트)를 호출하고 화면에 보여줌 <br> - ``ButtonOptionToggle`` 위젯을 사용하여 뉴스 국가 옵션 리스트를 화면에 보여줌 (옵션 선택 시 선택된 옵션을 기준으로 뉴스 리스트를 다시 호출함) <br> - 뉴스 즐겨찾기 기능(추가, 삭제) <br> <br>  **[즐겨찾기 스크린]** <br> - 즐겨찾기 리스트에 추가된 뉴스 리스트를 화면에 보여줌 <br>- 목록에서 특정 뉴스 아이템을 삭제할 수 있음                                                                                                                                                                                           <br><br>                    **[디테일 스크린]** <br> - 부모 스크린(홈스크린)에서 전달 받은 뉴스 아이템 속성들을 화면에 보여줌 |
| 기술 스택          | - Flutter                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Pain Point     | 1. Swift와 달리 Flutter에서 사용되는 ``class`` 에서 값을 초기화 하는 구조가 달라서 이해하는데 시간이 필요함(``struct``X). <br> <br> 2. MVVM패턴에서 Networking(API 연동)은 어떤 레이어에서 담당해야되는 고민이 되었음. <br/>                                                                                                                                                                                                                                                                                                                                                                                            |
| 아쉬운점 <br>& 느낀점 | 1. Swift 달리 Flutter에서는 ``struct`` 키워드를 사용할 수 없어서 ``model``과 ``viewModel``를 연동하는데 어려움이 있었음<br> <br/> 2. 이론적으로 ``MVVVM``패턴의 장점과 적용 방법에 대해 이해했지만, 실제 프로젝트에서 다른 구조에 비해 얼마나 이점을 가질 수 있는 정확한 판단을 하지 못하겠음. 아마 구조가 작은 프로젝트에서만 관련 패턴을 적용해서 그런듯<br/>                                                                                                                                                                                                                                                                                                                     |




## 학습한 내용
## MVVM Architecture 
## Model
- 2 가지 모델 타입을 적용
1. 호출하려는 데이터를 포맷팅하는 Model
```dart
class News {
  final String? title;
  final String? description;
  final String? urlToImage;

  News({this.title, this.description, this.urlToImage});

  factory News.fromJson(Map<String, dynamic> json) {
    return News(
      title: json['title'],
      description: json['description'],
      urlToImage: json['urlToImage'],
    );
  }
}
```

2. 내부 비즈니스 로직과 Data Store을 관리하는 모델

```dart
class NewsCore {
  List<News> newsList = [];
  List<News> favoriteNewsList = [];

  /* 뉴스 즐겨찾기 추가 & 삭제 */
  void manageFavoriteNews(News item) {
    // 조건 : 즐쳐찾기 배열에 기존 아이템이 있다면 '삭제'
    if (favoriteNewsList.contains(item)) {
      favoriteNewsList.remove(item);
      print("제거됨");
    } else {
      // 조건 : 기존 아이템이 없다면 '추가'
      favoriteNewsList.add(item);
      print("추가됨");
    }
  }
}
```


## VewModel
```dart
enum LoadingStatus { done, empty }

class NewsViewModel extends GetxController {
  LoadingStatus loadingStatus = LoadingStatus.empty;

  // Model과 연동
  NewsCore _model;
  NewsViewModel({required NewsCore model}) : _model = model;

  /* 인스턴스 */
  List<News> get newsLists {
    return _model.newsList;
  }

  List<News> get favoriteNewsList {
    return _model.favoriteNewsList;
  }

  /* Intent - 비즈니스 로직 */
  void manageFavoriteNews(News item) {
    _model.manageFavoriteNews(item);
    update(); // Important!!
  }

  /* 네트워킹 */
  void fetchNews(String country) async {
    loadingStatus = LoadingStatus.empty;
    update();
    List<News> newList = await NewsApi(country).fetchNews();
    _model.newsList = newList.toList();
    update();
    loadingStatus =
        _model.newsList.isEmpty ? LoadingStatus.empty : LoadingStatus.done;
    update();
  }
}
```
- ``Model``과 연동
    - 메소드와 인스턴스를 View에서 호출 할 수 있도록함
- ViewModel에서 네트워크를 호출함.
  
## View
```dart
class HomeScreen extends StatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

var viewModel = NewsViewModel(model: NewsCore());

class _HomeScreenState extends State<HomeScreen> {
  @override
  void initState() {
    super.initState();
    viewModel.fetchNews("kr"); // 기본 나라 : "Korea"
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: homeAppBar(),
      floatingActionButton: FloatingActionButton(
        // 즐겨찾기 스크린 Route
        onPressed: () {
          Get.to(const FavoriteNewsScreen());
        },
        child: const Icon(Icons.favorite),
      ),
      body: Padding(
        padding: const EdgeInsets.symmetric(horizontal: 20),
        child: GetBuilder<NewsViewModel>(
            init: viewModel,
            builder: (_) {
              return _.loadingStatus == LoadingStatus.empty
                  ? const Center(child: CircularProgressIndicator())
                  : NewsList(viewModel: _, itemList: _.newsLists);
            }),
      ),
    );
  }
```
- View Model 연동
- 네트워크 예외처리