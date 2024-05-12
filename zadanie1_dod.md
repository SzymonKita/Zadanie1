Punkty dodatkowe
Obraz został umieszczony na dockerhub w repozytorium podpiętym pod docker scout poleceniem:
docker buildx build -t szymonkita/lab8:zadanie1 --push --sbom=true --provenance=mode=max .
Usunięto większość zagrożeń zostało usunięte. Pozostał jeden problem, który nie posiada (w czasie wykonywania sprawozdania) wersji rozwiązującej problem tego obrazu, więc pozostał jeden zagrożony pakiet z oceną High
Zrzut ekranu umieszczono jako plik zadanie_dodatkowe.png

Część dodatkowa:
Utworzenie buildera wykorzystującego sterownik docker-container
docker buildx create --name zadaniebuilder –driver docker-container –bootstrap
Wybranie buildera jako domyślnego:
docker buildx user zadaniebuilder
Polecenie wykorzystujące builder i cache
docker buildx build -t szymonkita/lab8:zadanie1 --platform linux/amd64,li
nux/arm64 --cache-to=szymonkita/lab8:cache --cache-from=szymonkita/lab8:cache --push --sbom=true --provenance=mode=max .
