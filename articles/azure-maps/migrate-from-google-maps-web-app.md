---
title: チュートリアル:Google マップから Web アプリを移行する | Microsoft Azure Maps
description: Google マップから Microsoft Azure Maps に Web アプリを移行する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 08566283181a4bb15f77016834c4dc0dffc184b7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910881"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Google Maps から Web アプリを移行する

Google Maps を使用するほとんどの Web アプリでは、Google Maps V3 JavaScript SDK が使用されています。 Azure Maps Web SDK は、移行に適した Azure ベースの SDK です。 Azure Maps Web SDK を使用すると、インタラクティブ マップをカスタマイズして、Web アプリケーションまたはモバイル アプリケーションに独自のコンテンツや映像を表示することができます。 このコントロールには、WebGL が利用されているため、大きなデータ セットを高いパフォーマンスでレンダリングすることができます。 JavaScript または TypeScript を使用して、この SDK での開発を行います。

既存の Web アプリケーションを移行する場合は、Cesium、Leaflet、OpenLayers などのオープンソースのマップ コントロール ライブラリが使用されているかどうかを確認します。 それが使用されており、Azure Maps Web SDK を利用しない場合に、アプリケーションを移行するためのもう 1 つのオプションは、オープンソースのマップ コントロールを引き続き使用し、それを Azure Maps タイル サービス ([道路タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [衛星タイル](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)) に接続することです。 一般的に使用されるいくつかのオープンソースのマップ コントロール ライブラリで、Azure Maps を使用する方法の詳細を以下に示します。

- Cesium - Web 用の 3D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [ドキュメント](https://cesiumjs.org/)
- Leaflet – Web 用の軽量な 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [ドキュメント](https://leafletjs.com/)
- OpenLayers - プロジェクションをサポートする Web 用の 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [ドキュメント](https://openlayers.org/)

## <a name="key-features-support"></a>主なフィーチャーのサポート

次の表には、Google Maps V3 JavaScript SDK の主な API フィーチャーと、Azure Maps Web SDK での同様の API のサポートが示されています。

| Google Maps 機能     | Azure Maps Web SDK のサポート |
|-------------------------|:--------------------------:|
| マーカー                 | ✓                          |
| マーカー クラスタリング       | ✓                          |
| ポリラインと多角形    | ✓                          |
| データ レイヤー             | ✓                          |
| グラウンド オーバーレイ         | ✓                          |
| ヒート マップ               | ✓                          |
| タイル レイヤー             | ✓                          |
| KML レイヤー               | ✓                          |
| 描画ツール           | ✓                          |
| ジオコーダー サービス        | ✓                          |
| 方向サービス      | ✓                          |
| 距離マトリックス サービス | ✓                          |
| 標高サービス       | 対応予定                    |

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK の注目すべき相違点

注意すべき Google Maps と Azure Maps の Web SDK の主な相違点をいくつか以下に示します。

- Azure Maps Web SDK にアクセスするためのホスト エンドポイントが提供されるだけでなく、必要に応じて、Web SDK をアプリに埋め込むための NPM パッケージも使用可能です。 詳細については、こちらの[ドキュメント](how-to-use-map-control.md)を参照してください。 このパッケージには TypeScript 定義も含まれています。
- Azure Maps で Map クラスのインスタンスを作成した後、マップを操作する前にマップの `ready` または `load` イベントが発生するのをコードで待機する必要があります。 これにより、確実にマップ リソースがすべて読み込まれ、アクセスできる状態になります。
- 両方のプラットフォームで基本マップに同様のタイリング システムを使用しますが、Google Maps のタイルのディメンションは 256 ピクセルで、Azure Maps のタイルのディメンションは 512 ピクセルとなります。 そのため、Azure Maps で Google Maps と同じマップ ビューを取得するには、Google Maps で使用されるズーム レベルを、Azure Maps で 1 ずつ減算する必要があります。
- Google Maps では座標は "緯度, 経度" と呼ばれますが、Azure Maps では "経度, 緯度" が使用されます。 これは、ほとんどの GIS プラットフォームで従う標準の `[x, y]` に合わせて調整されます。
- Azure Maps Web SDK の図形は、GeoJSON スキーマに基づいています。 ヘルパー クラスは、["*atlas.data*" 名前空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)を通じて公開されます。 また、["*atlas.Shape*"](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) というクラスがあります。これを使用して、GeoJSON オブジェクトをラップし、データ バインド可能な方法で簡単に更新および保守できるようにします。
- Azure Maps の座標は、`[longitude, latitude]` または new atlas.data.Position(経度, 緯度) の形式でシンプルな数値配列として指定できる、Position オブジェクトとして定義されます。
    > [!TIP]
    > Position クラスには、"緯度, 経度" 形式の座標をインポートするための静的ヘルパー メソッドがあります。 [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) メソッドは、多くの場合、Google Maps コードの `new google.maps.LatLng` メソッドと置き換えることができます。
- マップに追加される各図形にスタイル設定情報を指定するのではなく、Azure Maps によってデータからスタイルが分離されます。 データはデータ ソースに格納され、Azure Maps コードでデータをレンダリングするために使用される、レンダリング レイヤーに接続されます。 この手法では、パフォーマンスが向上します。 さらに、多くのレイヤーでは、データドリブンのスタイル設定がサポートされます。これにより、ビジネス ロジックをレイヤー スタイル オプションに追加して、図形で定義されているプロパティに基づいてレイヤー内で個々の図形をレンダリングする方法を変更することができます。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK のサイドバイサイドの例

Web アプリケーションを Google Maps V3 JavaScript SDK から Azure Maps Web SDK に移行する際に役立つ一般的なユース ケースについて説明する、各プラットフォームのコード サンプルのコレクションを以下に示します。 Web アプリケーションに関連するコード サンプルは JavaScript で提供されています。しかし、Azure Maps では、[NPM モジュール](how-to-use-map-control.md)を通じて、追加オプションとして TypeScript 定義も提供されます。

### <a name="load-a-map"></a>マップを読み込む

両方の SDK のマップの読み込みでは、以下の同じ一連の手順に従います。

- Map SDK への参照を追加します。
- マップのプレースホルダーとして機能する `div` タグを、ページの本文に追加します。
- ページが読み込まれたときに呼び出される JavaScript 関数を作成します。
- それぞれのマップ クラスのインスタンスを作成します。

**いくつかの主な相違点**

- Google Maps では、API のスクリプト参照でアカウント キーを指定する必要があります。 Azure Maps の認証資格情報は、マップ クラスのオプションとして指定されます。 これは、サブスクリプション キーまたは Azure Active Directory 情報にすることができます。
- Google Maps では、マップを読み込むために初期化関数を呼び出すのに使用される、API のスクリプト参照でコールバック関数を受け取ります。 Azure Maps では、ページの onload イベントを使用する必要があります。
- マップがレンダリングされる `div` 要素を参照する場合、Azure Maps の `Map` クラスには `id` 値のみが必要ですが、Google Maps では `HTMLElement` オブジェクトが必要です。
- Azure Maps の座標は、`[longitude, latitude]` の形式でシンプルな数値配列として指定できる、位置オブジェクトとして定義されます。
- Azure Maps のズーム レベルは、Google Maps の例よりも 1 つ下のレベルとなります。これは、プラットフォーム間のタイリング システム サイズが異なるためです。
- 既定では、Azure Maps で、ズーム ボタンやマップ スタイル ボタンなどのナビゲーション コントロールはマップ キャンバスに追加されません。 しかし、マップ スタイル ピッカー、ズーム ボタン、コンパスまたは回転コントロール、およびピッチ コントロールを追加するためのコントロールがあります。
- イベント ハンドラーは、マップ インスタンスの `ready` イベントを監視するために Azure Maps に追加されます。 これは、マップで WebGL コンテキストおよび必要なすべてのリソースの読み込みが完了したときに発生します。 このイベント ハンドラーでは、任意のポスト ロード コードを追加できます。

以下の例では、ニューヨークが中心の基本マップの読み込み方法が示されています。これは、座標の経度が -73.985、緯度が 40.747 で、ズーム レベルが 12 の Google Maps の場合です。

**前: Google Maps**

次のコードは、ある場所を中心にズームされた Google Map を表示する方法の例です。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

<center>

![シンプルな Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**後: Azure Maps**

次のコードは、Azure Maps で同じビューのマップを、マップ スタイル コントロールおよびズーム ボタンと共に読み込む方法を示しています。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

<center>

![シンプルな Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Web アプリで Azure Maps マップ コントロールを設定して使用する方法に関する詳細なドキュメントについては、[こちら](how-to-use-map-control.md)を参照してください。

> [!NOTE]
> Google Maps とは異なり、Azure Maps では、マップの読み込み時に最初の中心とズーム レベルを指定する必要はありません。 マップの読み込み時にこの情報が提供されていない場合は、マップで、ユーザーが存在する都市の特定が試行され、そこでマップの中心とズームの設定が行われます。

**その他のリソース:**

- Azure Maps では、マップ ビューの回転とピッチを行うためのナビゲーション コントロールも提供されます ([こちら](map-add-controls.md)を参照してください)。

### <a name="localizing-the-map"></a>マップのローカライズ

対象ユーザーが複数の国に分散している場合や、使用されている言語が異なる場合は、ローカライズが重要になります。

**前: Google Maps**

Google Maps をローカライズするには、言語と地域のパラメーターを追加します

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

以下に、言語が "fr-fr" に設定されている Google Maps の例を示します。

<center>

![Google Maps のローカライズ](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**後: Azure Maps**

Azure Maps には、マップの言語と地域ビューを設定するための 2 つの異なる方法が用意されています。 最初のオプションでは、この情報をグローバルな *atlas* 名前空間に追加します。これにより、アプリ内のすべてのマップ コントロール インスタンスによって、これらの設定が既定で使用されます。 以下では、言語をフランス語 ("fr-FR") に、地域ビューを "auto" に設定します。

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

2 つ目のオプションでは、次のようにマップを読み込むときに、この情報をマップ オプションに渡します。

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Azure Maps を使用すると、言語と地域の設定が異なる複数のマップ インスタンスを同じページに読み込むことができます。 また、マップでは読み込み後に、これらの設定を更新することもできます。 Azure Maps でサポートされている言語の詳細な一覧については、[こちら](supported-languages.md)を参照してください。

言語が "fr" に設定され、ユーザーの地域が "fr-FR" に設定されている Azure Maps の例を以下に示します。

<center>

![Azure Maps のローカライズ](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>マップ ビューの設定

Azure および Google Maps の両方の動的マップは、JavaScript で適切な関数を呼び出すことによって、プログラムで新しい地理的な場所に移動できます。 以下の例では、マップに衛星航空画像を表示し、マップの中心をある場所に指定する方法を示しています。これは、座標の経度が -111.0225、緯度が 35.0272 で、ズーム レベルを 15 に変更する Google Maps の場合です。

> [!NOTE]
> Google Maps では、ディメンションが 256 ピクセルのタイルが使用されますが、Azure Maps では、より大きな 512 ピクセルのタイルが使用されます。 これにより、Azure Maps で Google Maps と同じマップ領域を読み込むために必要なネットワーク要求の数が減ります。 しかし、マップ コントロールでのタイル ピラミッドの動作方法により、Azure Maps のより大きなタイルは、Google Maps のマップと同じ可視領域を得るために、Azure Maps の使用時に Google Maps で利用されるズーム レベルを 1 ずつ減算する必要があることを意味します。

**前: Google Maps**

Google Maps マップ コントロールは、`setOptions` メソッドを使用してプログラムで移動できます。これにより、マップの中心とズーム レベルを指定できます。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps の設定ビュー](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**後: Azure Maps**

Azure Maps では、マップの `setCamera` メソッドを使用して、マップの位置をプログラムで変更し、`setStyle` メソッドを使用して、マップ スタイルを変更することができます。 Azure Maps 内の座標は "経度, 緯度" の形式であり、ズーム レベルの値は 1 ずつ減算されることに注意してください。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps の設定ビュー](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**その他のリソース:**

- [マップ スタイルを選択する](choose-map-style.md)
- [サポートされているマップ スタイル](supported-map-styles.md)

### <a name="adding-a-marker"></a>マーカーの追加

Azure Maps には、ポイント データをマップにレンダリングできる方法が複数あります。

- **HTML マーカー** – 従来の DOM 要素を使用してポイントをレンダリングします。 HTML マーカーではドラッグがサポートされます。
- **シンボル レイヤー** – WebGL コンテキスト内でアイコンやテキストを使用してポイントをレンダリングします。
- **バブル レイヤー** - ポイントをマップ上で円としてレンダリングします。 円の半径は、データのプロパティに基づいてスケーリングできます。

シンボルおよびバブル レイヤーの両方は、WebGL コンテキスト内にレンダリングされ、マップ上で非常に大きなポイント セットをレンダリングできます。 これらのレイヤーでは、データをデータ ソースに格納する必要があります。 `ready` イベントが発生した後、データ ソースとレンダリング レイヤーをマップに追加する必要があります。 HTML マーカーは、ページ内に DOM 要素としてレンダリングされ、データ ソースは使用されません。 ページに含まれる DOM 要素が多いほど、そのページの速度は遅くなります。 マップ上で数百を超えるポイントをレンダリングする場合は、代わりにレンダリング レイヤーのいずれかを使用することをお勧めします。

次の例では、マップにマーカーを追加します。ここでは、経度が -0.2、緯度が 51.5 で、オーバーレイ ラベルの数値は 10 になっています。

**前: Google Maps**

Google Maps では、`google.maps.Marker` クラスを使用し、マップをオプションの 1 つとして指定することで、マーカーがマップに追加されます。

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps のマーカー](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**後: HTML マーカーを使用した Azure Maps**

Azure Maps では、HTML マーカーを使用してマップ上のポイントを表示することができ、単に、マップ上に少数のポイントのみを表示する必要があるアプリの場合にお勧めます。 HTML マーカーを使用するには、単に `atlas.HtmlMarker` クラスのインスタンスを作成し、テキストおよび位置のオプションを設定して、`map.markers.add` メソッドを使ってマップにマーカーを追加します。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps の HTML マーカー](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**後: シンボル レイヤーを使用した Azure Maps**

シンボル レイヤーを使用する場合は、データをデータ ソースに追加し、データ ソースをレイヤーにアタッチする必要があります。 さらに、`ready` イベントが発生した後、データ ソースとレイヤーをマップに追加する必要があります。 一意のテキスト値をシンボルの上にレンダリングするには、テキスト情報をデータ ポイントのプロパティとして格納し、そのプロパティをレイヤーの `textField` オプションで参照する必要があります。 これは HTML マーカーを使用するよりも少し手間がかかりますが、多くのパフォーマンスの利点が得られます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps のシンボル レイヤー](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**その他のリソース:**

- [データ ソースを作成する](create-data-source-web-sdk.md)
- [シンボル レイヤーを追加する](map-add-pin.md)
- [バブル レイヤーを追加する](map-add-bubble-layer.md)
- [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
- [HTML マーカーを追加する](map-add-custom-html.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
- [シンボル レイヤーのアイコン オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [シンボル レイヤーのテキスト オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML マーカーのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML マーカーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>カスタム マーカーの追加

カスタム イメージを使用すると、マップ上のポイントを表すことができます。 以下のイメージ例では、カスタム イメージを使用してマップ上のポイントを表示します。ここでは、緯度が 51.5、経度が -0.2 で、マーカーの位置をオフセットして、画鋲アイコンのポイントがマップ上の正しい位置に揃うようにします。

<center>

![黄色の画鋲のイメージ](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**前: Google Maps**

Google Maps では、カスタム マーカーが作成されます。ここでは、イメージへの `url` と、画鋲イメージのポイントをマップ上の座標に合わせるための `anchor` ポイントを含む、`Icon` オブジェクトを指定します。 イメージの左上隅を基準とした、Google Maps のアンカー値。

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps のカスタム マーカー](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**後: HTML マーカーを使用した Azure Maps**

Azure Maps で HTML マーカーをカスタマイズする場合は、HTML `string` または `HTMLElement` をマーカーの `htmlContent` オプションに渡すことができます。 Azure Maps では、`anchor` オプションを使用して、9 つの定義済みの参照ポイントである "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" のいずれかを使用して、位置座標を基準とするマーカーの相対位置を指定します。 既定では、コンテンツは html コンテンツの下中央に固定されます。 Google Maps からコードをより簡単に移行できるようにするには、`anchor` を "top-left" に設定してから、Google Maps で使用したものと同じオフセットで `pixelOffset` オプションを使用します。 Azure Maps のオフセットは、Google Maps の反対方向に移動するため、-1 を乗算します。

> [!TIP]
> html コンテンツにスタイルとして `pointer-events:none` を追加して、不要なアイコンを表示する、Microsoft Edge での既定のドラッグ動作を無効にします。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps のカスタム HTML マーカー](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**後: シンボル レイヤーを使用した Azure Maps**

Azure Maps のシンボル レイヤーでもカスタム イメージがサポートされますが、最初にイメージをマップ リソースに読み込んで、一意の ID を割り当てる必要があります。 その後、シンボル レイヤーでこの ID を参照できます。 アイコンの `offset` オプションを使用して、イメージ上の正しいポイントに合わせるために、シンボルをオフセットできます。 Azure Maps では、`anchor` オプションを使用して、9 つの定義済みの参照ポイントである "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" のいずれかを使用して、位置座標を基準とするシンボルの相対位置を指定します。 既定では、コンテンツは html コンテンツの下中央に固定されます。 Google Maps からコードをより簡単に移行できるようにするには、`anchor` を "top-left" に設定してから、Google Maps で使用したものと同じオフセットで `offset` オプションを使用します。 Azure Maps のオフセットは、Google Maps の反対方向に移動するため、-1 を乗算します。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps のカスタム アイコン シンボル レイヤー](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> ポイントの高度なカスタム レンダリングを作成するには、複数のレンダリング レイヤーを一緒に使用します。 たとえば、異なる色付きの円で同じアイコンの画鋲を複数使用する場合は、色ごとに多くのイメージを作成するのではなく、バブル レイヤーの上にシンボル レイヤーをオーバーレイし、同じデータ ソースを参照するようにします。 これは作成するよりもはるかに効率的であり、マップに多くのさまざまなイメージが保持されます。

**その他のリソース:**

- [データ ソースを作成する](create-data-source-web-sdk.md)
- [シンボル レイヤーを追加する](map-add-pin.md)
- [HTML マーカーを追加する](map-add-custom-html.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
- [シンボル レイヤーのアイコン オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [シンボル レイヤーのテキスト オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML マーカーのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML マーカーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>ポリラインの追加

ポリラインは、マップ上の線またはパスを表すために使用されます。 次の例では、マップ上に破線のポリラインを作成する方法を示します。

**前: Google Maps**

Google Maps では、ポリライン クラスで一連のオプションを受け取ります。 座標の配列は、ポリラインの `path` オプションで渡されます。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps のポリライン](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**後: Azure Maps**

Azure Maps では、ポリラインを LineString または MultiLineString オブジェクトと呼びます。 これらのオブジェクトは、データ ソースに追加し、線レイヤーを使用してレンダリングできます。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps のポリライン](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**その他のリソース:**

- [マップに線を追加する](map-add-line-layer.md)
- [線レイヤーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>多角形の追加

多角形は、マップ上の領域を表すために使用されます。 Azure Maps と Google Maps では、多角形について、よく似たサポートが提供されます。 次の例は、マップの中心座標に基づいて、三角形を形成する多角形を作成する方法を示しています。

**前: Google Maps**

Google Maps では、多角形クラスで一連のオプションを受け取ります。 座標の配列は、多角形の `paths` オプションで渡されます。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps の多角形](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**後: Azure Maps**

Azure Maps では、Polygon および MultiPolygon オブジェクトをデータ ソースに追加し、レイヤーを使用してマップ上にレンダリングすることができます。 多角形の領域は、多角形レイヤーでレンダリングできます。 多角形の枠線は、線レイヤーを使用してレンダリングできます。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps の多角形](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**その他のリソース:**

- [マップに多角形を追加する](map-add-shape.md)
- [マップに円を追加する](map-add-shape.md#add-a-circle-to-the-map)
- [多角形レイヤーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [線レイヤーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>情報ウィンドウを表示する

エンティティの追加情報は、Google Maps では `google.maps.InfoWindow` クラスとしてマップに表示できます。Azure Maps では、`atlas.Popup` クラスを使用して、これを行うことができます。 次の例では、マップにマーカーを追加し、クリックすると、情報ウィンドウやポップアップが表示されるようにします。

**前: Google Maps**

Google Maps では、`google.maps.InfoWindow` コンストラクターを使用して、情報ウィンドウが作成されます。

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps のポップアップ](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**後: Azure Maps**

Azure Maps では、ポップアップを使用して、場所の追加情報を表示できます。 HTML の `string` または `HTMLElement` オブジェクトを、ポップアップの `content` オプションに渡すことができます。 ポップアップは、必要に応じて、図形とは別に表示できるため、`position` 値を指定する必要があります。 ポップアップを表示するには、`open` メソッドを呼び出し、ポップアップを表示する `map` を渡します。

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps のポップアップ](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> シンボル、バブル、線または多角形のレイヤーで同じことを行うには、マーカーではなく、単にマップのイベント コードにレイヤーを渡します。

**その他のリソース:**

- [ポップアップを追加する](map-add-popup.md)
- [メディア コンテンツを含むポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [図形のポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [複数のピンでのポップアップの再利用](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [ポップアップのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [ポップアップのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>GeoJSON ファイルをインポートする

Google Maps では、`google.maps.Data` クラスを使用した GeoJSON データの読み込みと動的なスタイル設定がサポートされています。 このクラスの機能は、Azure Maps のデータドリブンのスタイル設定とより多くの点で一致しています。 1 つの主な違いは、Google Maps では、コールバック関数と、UI スレッドで個別に処理された各フィーチャーをスタイル設定するビジネス ロジックを指定することです。 Azure Maps では、レイヤーでデータドリブン式がスタイル設定オプションとして指定することがサポートされます。 これらの式は、レンダリング時に別のスレッドで処理され、レンダリングのパフォーマンスが向上し、より大きなデータ セットをより速くレンダリングできます。

次の例では、USGS から過去 7 日間のすべての地震の GeoJSON フィードを読み込んで、マップ上にスケーリングされた円としてレンダリングします。 各円の色とスケールは、データ セット内の各フィーチャーの `"mag"` プロパティに格納されている、各地震のマグニチュードに基づいています。 マグニチュードが 5 以上の場合、円は赤色になり、3 以上 5 未満の場合、円はオレンジ色になり、3 未満の場合、円は緑色になります。 各円の半径は、0.1 を乗算したマグニチュードの指数になります。

**前: Google Maps**

Google Maps では、`map.data.setStyle` メソッドに 1 つのコールバック関数を指定できます。これは、`map.data.loadGeoJson` メソッドを介して GeoJSON フィードから読み込まれた各フィーチャーにビジネス ロジックを適用するために使用されます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps の GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**後: Azure Maps**

GeoJSON は Azure Maps の基本データ型であり、`datasource.importFromUrl` メソッドを使用してデータ ソースに簡単にインポートできます。 バブル レイヤーでは、データ ソース内のフィーチャーのプロパティに基づいて、スケーリングされた円をレンダリングするための機能が提供されます。 コールバック関数を使用するのではなく、ビジネス ロジックが式に変換され、スタイル オプションに渡されます。 式では、ビジネス ロジックを別のスレッドに渡し、フィーチャー データに対して評価できるように、ビジネス ロジックの動作を定義します。 複数のデータ ソースとレイヤーを Azure Maps に追加して、それぞれ異なるビジネス ロジックを使用できるため、複数のデータ セットをさまざまな方法でマップにレンダリングできます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps の GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**その他のリソース:**

- [シンボル レイヤーを追加する](map-add-pin.md)
- [バブル レイヤーを追加する](map-add-bubble-layer.md)
- [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>マーカー クラスタリング

多数のデータ ポイントをマップ上に視覚化すると、ポイントが互いに重なり合い、マップの見た目がごちゃごちゃして、見づらく使いづらいものになります。 ポイント データのクラスタリングは、このユーザー エクスペリエンスを向上させ、パフォーマンスを向上させるためにも使用できます。 ポイント データのクラスタリングは、互いに近いポイント データを結合し、単一のクラスター化されたデータ ポイントとしてマップ上に表現するプロセスです。 ユーザーがマップにズーム インすると、クラスターは個々のデータ ポイントに分解します。

次の例では、過去 1 週間の地震データの GeoJSON フィードを読み込み、それをマップに追加します。 クラスターは、含まれているポイントの数に応じて、スケーリングされた色付きの円としてレンダリングされます。

> [!NOTE]
> マーカー クラスタリングには、いくつかの異なるアルゴリズムが使用されています。 Google および Azure Maps では、若干異なるアルゴリズムが使用されます。 そのため、クラスター内のポイントの分布が異なる場合があります。

**前: Google Maps**

Google Maps では、マーカーを、MarkerClusterer ライブラリに読み込んでクラスタリングできます。 クラスター アイコンは、名前が 1 から 5 の数値で、同じディレクトリにホストされているイメージに限定されます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps のクラスタリング](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**後: Azure Maps**

Azure Maps では、データはデータ ソースによって追加および管理されます。 レイヤーはデータ ソースに接続され、そこでデータがレンダリングされます。 Azure Maps の `DataSource` クラスでは、いくつかのクラスタリング オプションが提供されます。

- `cluster` – データ ソースにポイント データをクラスタリングするように指示します。
- `clusterRadius` -ポイントをまとめてクラスタリングする場合の半径 (ピクセル単位)。
- `clusterMaxZoom` - クラスタリングが行われる最大ズーム レベル。 これを超えてズームインすると、すべてのポイントがシンボルとしてレンダリングされます。
- `clusterProperties` - 各クラスター内のすべてのポイントに対して式を使用して計算され、各クラスター ポイントのプロパティに追加されるカスタム プロパティを定義します。

クラスタリングが有効になっている場合、データ ソースでは、クラスタリングおよびクラスタリング解除されたデータ ポイントをレンダリングするためにレイヤーに送信します。 データ ソースでは、数十万のデータ ポイントをクラスタリングすることができます。 クラスタリングされたデータ ポイントには、次のプロパティがあります。

| プロパティ名             | 種類    | [説明]   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | フィーチャーがクラスターを表すかどうかを示します。 |
| `cluster_id`              | string  | DataSource の `getClusterExpansionZoom`、`getClusterChildren`、および `getClusterLeaves` メソッドで使用できるクラスターの一意な ID。 |
| `point_count`             | number  | クラスターに含まれているポイントの数。  |
| `point_count_abbreviated` | string  | `point_count` の値が長い場合にその値を省略形にした文字列。 (たとえば、4,000 が 4K になります)  |

`DataSource` クラスには、`cluster_id` を使用してクラスターに関する追加情報にアクセスするための次のヘルパー関数があります。

| 方法 | の戻り値の型 : | [説明] |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 次のズーム レベルで指定されたクラスターの子を取得します。 これらの子はシェイプとサブクラスターの組み合わせの場合があります。 サブクラスターは ClusteredProperties と一致するプロパティを持つフィーチャーになります。 |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | クラスターが拡大し始めるか、または分解するズーム レベルを計算します。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | クラスター内のすべてのポイントを取得します。 ポイントのサブセットを返すには `limit` を設定し、ポイントをページ送りするには `offset` を使用します。 |

マップ上でクラスタリングされたデータをレンダリングするときは、多くの場合、2 つ以上のレイヤーを使用するのが最も簡単です。 次の例では、3 つのレイヤーを使用します。つまり、クラスターのサイズに基づいてスケーリングされた色付き円を描画するためのバブル レイヤー、クラスター サイズをテキストとしてレンダリングするためのシンボル レイヤー、およびクラスタリング解除されたポイントをレンダリングするための 2 番目のシンボル レイヤーです。 Azure Maps でクラスタリングされたデータをレンダリングする方法は他にも多数あります (詳しくは、[ポイント データのクラスタリング](clustering-point-data-web-sdk.md)に関するドキュメントを参照)。

GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して、Azure Maps に直接インポートできます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps のクラスタリング](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**その他のリソース:**

- [シンボル レイヤーを追加する](map-add-pin.md)
- [バブル レイヤーを追加する](map-add-bubble-layer.md)
- [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>ヒート マップを追加する

ヒート マップは、点密度マップとも呼ばれ、一定の範囲の色を使用して、データの密度を表すために使用されるデータ視覚化の一種です。 多くの場合、マップ上のデータの "ホットスポット" を示すために使用され、大規模なポイント データ セットをレンダリングする場合に適しています。

次の例では、USGS から過去 1 か月のすべての地震の GeoJSON フィードを読み込み、重みとして `"mag"` プロパティが使用される加重ヒート マップとして、それらをレンダリングします。

**前: Google Maps**

Google Maps では、ヒート マップを作成する場合に、API スクリプト URL に `&libraries=visualization` を追加して、"視覚エフェクト" ライブラリを読み込む必要があります。 Google Maps のヒート マップ レイヤーでは、GeoJSON データが直接サポートされません。代わりに、データを最初にダウンロードし、加重データ ポイントの配列に変換する必要があります。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps のヒートマップ](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**後: Azure Maps**

Azure Maps で、GeoJSON データをデータ ソースに読み込み、そのデータ ソースをヒート マップ レイヤーに接続します。 重みに使用されるプロパティは、式を使用して `weight` オプションに渡すことができます。 GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して、Azure Maps に直接インポートできます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps のヒートマップ](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**その他のリソース:**

- [ヒート マップ レイヤーを追加する](map-add-heat-map-layer.md)
- [ヒート マップ レイヤーのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [ヒート マップ レイヤーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>タイル レイヤーをオーバーレイする

タイル レイヤー (Google Maps ではイメージ オーバーレイとも呼ばれる) を使用すると、マップ タイリング システムに合わせて、より小さなタイル表示されたイメージに分割されたより大きなイメージをオーバーレイすることができます。 これは、大きなイメージや非常に大きなデータ セットをオーバーレイする一般的な方法です。

次の例では、アイオワ州立大学の Iowa Environmental Mesonet の気象レーダー タイル レイヤーをオーバーレイします。

**前: Google Maps**

Google Maps では、`google.maps.ImageMapType` クラスを使用して、タイル レイヤーを作成できます。

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps のタイル レイヤー](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**後: Azure Maps**

Azure Maps では、他のレイヤーとほぼ同じように、タイル レイヤーをマップに追加できます。 x、y、ズーム プレースホルダー(`{x}`、`{y}`、`{z}`) の書式設定された URL はそれぞれ、タイルにアクセスする場所をレイヤーに指示するために使用されます。 Azure Maps のタイル レイヤーでは、`{quadkey}`、`{bbox-epsg-3857}` および `{subdomain}` プレースホルダーもサポートされます。

> [!TIP]
> Azure Maps では、レイヤーは、基本マップ レイヤーを含む、他のレイヤーの下に簡単にレンダリングできます。 多くの場合、読みやすいようにタイル レイヤーをマップ ラベルの下にレンダリングすることをお勧めします。 `map.layers.add` メソッドでは、2 番目のパラメーターを受け取ります。これは、下に新しいレイヤーを挿入するレイヤーの id です。 マップ ラベルの下にタイル レイヤーを挿入する場合は、次のコードを使用できます: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps のタイル レイヤー](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> タイル要求は、マップの `transformRequest` オプションを使用してキャプチャできます。 これにより、必要に応じて、要求に対してヘッダーの変更や追加を行うことができます。

**その他のリソース:**

- [タイル レイヤーを追加する](map-add-tile-layer.md)
- [タイル レイヤーのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [タイル レイヤーのオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>交通情報を表示する

交通データは、Azure および Google Maps の両方にオーバーレイできます。

**前: Google Maps**

Google Maps では、交通レイヤーを使用して、交通データをマップにオーバーレイすることができます。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps の交通情報](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**後: Azure Maps**

Azure Maps では、交通情報を表示するためのさまざまなオプションが提供されます。 道路の閉鎖や事故などの交通事故は、マップ上にアイコンとして表示できます。 交通流量 (色分けされた道路) をマップ上にオーバーレイでき、掲示された速度制限、通常の予想される遅延、または絶対遅延を基準として、色を変更することができます。 Azure Maps の事故データは 1 分ごとに、流量データは 2 分ごとに更新されます。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps の交通情報](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Azure Maps の交通アイコンのいずれかをクリックすると、ポップアップに追加情報が表示されます。

<center>

![Azure Maps の交通事故](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**その他のリソース:**

- [マップ上にトラフィックを表示する](map-show-traffic.md)
- [交通情報オーバーレイのオプション](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>グラウンド オーバーレイを追加する

Azure および Google Maps の両方では、マップのパンとズームを行うときに移動およびスケーリングできるように、マップ上のジオリファレンスされたイメージをオーバーレイすることがサポートされています。 Google Maps では、これらはグラウンド オーバーレイとして知られており、Azure Maps では、イメージ レイヤーと呼ばれます。 これらは、ビルのフロア プラン、古いマップのオーバーレイ、またはドローンからのイメージに適しています。

**前: Google Maps**

Google Maps でグラウンド オーバーレイを作成する場合は、オーバーレイするイメージへの URL と、マップ上のイメージをバインドするための境界ボックスを指定する必要があります。 この例では、マップ上に [1922 年のニュージャージー州のニューアーク](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)のマップ イメージをオーバーレイします。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

<center>

![Google Maps のイメージ オーバーレイ](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**後: Azure Maps**

Azure Maps では、`atlas.layer.ImageLayer` クラスを使用して、ジオリファレンスされたイメージをオーバーレイできます。 このクラスでは、イメージへの URL と、イメージの四隅の座標のセットが必要です。 イメージは、同じドメインでホストされているか、CORS が有効になっている必要があります。

> [!TIP]
> イメージの各隅の座標ではなく、北、南、東、西および回転の情報のみがある場合は、静的な [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) メソッドを使用できます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps のイメージ オーバーレイ](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**その他のリソース:**

- [イメージをオーバーレイする](map-add-image-layer.md)
- [イメージ レイヤーのクラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>その他のコード サンプル

Google Maps の移行に関連するその他のコード サンプルを以下にいくつか示します。

- [描画ツール](map-add-drawing-toolbar.md)
- [マップを 2 本指でパンするように制限する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [スクロール ホイールのズームを制限する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [全画面表示コントロールを作成する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**サービス**

- [Azure Maps サービス モジュールの使用](how-to-use-services-module.md)
- [観光名所の検索](map-search-location.md)
- [座標から情報を取得する (逆ジオコード)](map-get-information-from-coordinate.md)
- [A から B までのルートを表示する ](map-route.md)
- [JQuery UI を使用する自動提案検索](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 から Azure Maps Web SDK へのクラスのマッピング

次の付録では、Google Maps V3 で最もよく使用されるクラスから、それに相当する Azure Maps Web SDK への相互参照マッピングを示します。

### <a name="core-classes"></a>コア クラス

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>オーバーレイ クラス

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | 「[マップに円を追加する](map-add-shape.md#add-a-circle-to-the-map)」を参照してください                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>サービス クラス

Azure Maps Web SDK には、個別に読み込むことができる[サービス モジュール](how-to-use-services-module.md)が含まれています。 このモジュールでは、Web API で Azure Maps REST サービスをラップし、JavaScript、TypeScript および Node.js アプリケーションで使用できます。

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>ライブラリ

ライブラリでは、マップにさらに機能が追加されます。 これらの多くは、Azure Maps のコア SDK に含まれています。 これらの Google Maps ライブラリの代わりに使用される、同等のクラスをいくつか以下に示します

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| 描画ライブラリ       | [描画ツール モジュール](set-drawing-options.md) |
| ジオメトリ ライブラリ      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 視覚エフェクト ライブラリ | [ヒート マップ レイヤー](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>次のステップ

Azure Maps Web SDK の詳細について学習します。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [サービス モジュールの使用方法](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [描画ツール モジュールの使用方法](set-drawing-options.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)

