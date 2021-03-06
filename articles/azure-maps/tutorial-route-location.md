---
title: 使用 Azure Maps 查找路线 | Microsoft Docs
description: 使用 Azure Maps 查找前往兴趣点的路线
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fda234b882cbf4a155881895bbf8401fe3ff3aca
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645023"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>使用 Azure Maps 查找前往兴趣点的路线

本教程介绍如何使用 Azure Maps 帐户和路线服务 SDK 来查找前往兴趣点的路线。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 设置地址坐标
> * 在路线服务中查询兴趣点的方向

## <a name="prerequisites"></a>先决条件

在继续之前，按照前面的教程中的步骤[创建 Azure Maps 帐户](./tutorial-search-location.md#createaccount)并且[获取帐户的订阅密钥](./tutorial-search-location.md#getkey)。

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>创建新地图

以下步骤说明如何使用 Map Control API 创建一个嵌入式静态 HTML 页面。

1. 在本地计算机上，创建一个新文件并将其命名为 **MapRoute.html**。
2. 将以下 HTML 组件添加到该文件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css"/>
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML 标头如何为 Azure Maps 库嵌入 CSS 和 JavaScript 文件的资源位置。 HTML 文件正文的脚本段将包含用于访问 Azure Maps API 的内联 JavaScript 代码。

3. 将以下 JavaScript 代码添加到 HTML 文件的 *script* 块。 使用从 Maps 帐户复制的主要密钥替换字符串 \<your account key\>。

    ```JavaScript
    // Instantiate map to the div with id "map"
    atlas.setSubscriptionKey("<your account key>");
    var map = new atlas.Map("map");
    ```

    Atlas.Map 提供一个可视和交互式的 Web 地图控件，它是 Azure Map Control API 的一个组件。

4. 保存文件并在浏览器中打开它。 此时，你有一个可以进一步开发的基础地图。

   ![查看基础地图](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>设置起点和终点

在本教程中，将起点设为 Microsoft，将终点设为西雅图的加油站。

1. 在“MapRoute.html”文件的相同脚本块中，添加以下 JavaScript 代码用于创建路线的起点和终点：

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    此代码创建两个 [GeoJSON 对象](https://en.wikipedia.org/wiki/GeoJSON)来表示路线的起点和终点。

2. 添加以下 JavaScript 代码，将起点和终点的图钉添加到地图中：

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    map.events.add("load", function () { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    map.setCameraBounds 根据起点和终点的坐标调整地图窗口。 **map.events.add** 可确保在完全加载地图后加载添加到地图的所有地图函数。 事件侦听器中的 API **map.addPins** 将这些点作为视觉组件添加到地图控件中。

3. 保存“MapRoute.html”文件并刷新浏览器。 现在，地图的中心为西雅图，可以看到标记起点的圆形蓝色图钉和标记终点的蓝色图钉。

   ![查看标记了起点和终点的地图](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>获取方向

本部分介绍如何使用 Maps 路线服务 API 查找从给定起点到目的地的路线。 路线服务提供多个 API，规划两个地点之间最快、最短、环保或令人兴奋的路线。 此外，它还允许用户使用 Azure 广泛的历史交通数据库和预测任何一天任何时间的路线时间来规划路线。 有关详细信息，请参阅 [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)（获取路线指示）。 应该**在 map load eventListener 中**添加以下所有功能，以确保它们在地图完全加载之后加载。

1. 首先，在地图上添加一个新层以显示路线路径或 linestring。 将以下 JavaScript 代码添加到 *script* 块。

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. 通过向脚本块添加以下 Javascript 代码来实例化客户端服务。
    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. 添加以下代码块来构造路由查询字符串。
    ```JavaScript
    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];
    ```

4. 若要获取路由，请向脚本中添加以下代码块。 它通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法查询 Azure Maps 路由服务，然后使用 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 将响应解析为 GeoJSON 格式。 然后，它将所有响应线条添加到地图来呈现路线。 有关更多信息，可以参阅[在地图上添加线条](./map-add-shape.md#addALine)。

    ```JavaScript
    // Execute the query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
         // Parse the response into GeoJSON
         var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

         // Get the first in the array of routes and add it to the map
         map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
             name: routeLinesLayerName
         });
    });
    ```

5. 保存“MapRoute.html”文件并刷新 web 浏览器。 要成功连接 Maps API，应看到类似于以下内容的地图。

    ![Azure 地图控件和路线服务](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Map Control API 创建新网页
> * 设置地址坐标
> * 在路线服务中查询兴趣点的方向

可以在此处访问本教程的代码示例：

> [使用 Azure Maps 查找路线](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/route.html)

下一教程演示如何在有限制的情况下（例如出行模式或货物类型）创建路线查询，以及如何在同一地图上显示多条路线。

> [!div class="nextstepaction"]
> [查找不同出行模式的路线](./tutorial-prioritized-routes.md)
