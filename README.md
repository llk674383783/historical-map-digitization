# 历史地图数字化工具集

这是一个面向历史地图、旧版地形图、方志地图和早期遥感图像的纯前端数字化工具集。项目以静态网页形式运行，不依赖后端服务，可直接部署到 GitHub Pages、对象存储静态网站或任意静态 Web 服务器。

工具集包含两个核心工具：

- 历史地图标注工具：在高分辨率地图图像上添加、编辑、检索文字注记，并导出像素坐标 JSON。
- 在线地理配准工具：通过控制点将历史地图像素坐标转换为现代经纬度坐标，并可导出 TFW、控制点文件、GeoJSON 等结果。

两个工具可以独立使用，也可以串联形成完整工作流：先在标注工具中提取地图上的文字信息，再在配准工具中将这些像素标注转换为标准地理空间数据。

## 页面结构

- `index.html`：项目首页，提供工具入口和功能介绍。
- `historical_map_annotator_compact_page.html`：历史地图标注工具。
- `Georeferencing_page.html`：在线地理配准工具。
- `.nojekyll`：用于 GitHub Pages，避免静态文件被 Jekyll 处理。

## 核心能力

### 历史地图标注

标注工具用于把历史地图中的地名、文字、符号说明等内容转化为结构化数据。

主要功能：

- 加载本地图像，支持常见图片格式以及 GeoTIFF。
- 打开云端共享地图库，按图名或标注内容检索地图。
- 基于 OpenSeadragon 浏览超大分辨率图像，支持平滑缩放和平移。
- 在图像上添加文字注记，记录精确像素坐标。
- 支持横排、竖排、字号、字距、透明度和旋转等样式调整。
- 支持按地名或别名进行模糊检索，点击结果可快速定位。
- 支持导入和导出 JSON，便于保存、继续编辑和进入配准流程。

### 在线地理配准

配准工具用于建立历史地图像素坐标与现代地理坐标之间的转换关系。

主要功能：

- 左侧加载历史地图，右侧加载现代天地图底图。
- 通过控制点模式，在历史地图和现代地图上成对选点。
- 使用控制点解算一阶仿射变换参数。
- 支持导入和导出控制点文件。
- 支持导入 TFW 坐标文件。
- 支持配准叠加预览，快速检查历史地图与现代底图是否对齐。
- 支持导入标注工具生成的 JSON，并导出 GeoJSON 点数据。
- 支持下载 TFW 文件，供 GIS 软件继续使用。

注意：页面中提供二阶、三阶选项用于残差评估和误差参考；当前用于 TFW、叠加预览和 GeoJSON 转换的核心参数仍是一阶仿射变换。

## 推荐工作流

### 1. 标注历史地图

1. 打开 `historical_map_annotator_compact_page.html`。
2. 点击“载入本地图像”，选择历史地图图片。
3. 勾选“开启编辑”。
4. 点击“添加注记”，在地图上单击需要标注的位置。
5. 输入地名或文字内容。
6. 根据地图排版调整字号、字距、横排/竖排、旋转角度和透明度。
7. 使用搜索框检查标注是否可检索。
8. 点击“导出 JSON”，保存标注数据。

导出的 JSON 会记录每条标注的像素坐标、文字内容和样式信息。

### 2. 配准历史地图

1. 打开 `Georeferencing_page.html`。
2. 点击“打开图片”，加载同一张历史地图。
3. 点击“添加控制点模式”。
4. 在左侧历史地图上选择一个可识别位置。
5. 在右侧现代地图上选择对应位置。
6. 重复添加至少 3 组控制点。
7. 查看 RMS 误差，必要时删除误差较大的点并重新选点。
8. 点击“叠加预览”，检查地图是否对齐。
9. 点击“下载 .tfw 文件”保存配准参数。

### 3. 将标注转换为 GeoJSON

1. 在配准工具中完成控制点解算，或导入已有 TFW 文件。
2. 点击“导入原标注”，选择标注工具导出的 JSON 文件。
3. 确认左侧历史地图上能看到导入标注。
4. 点击“导出 GeoJSON”。
5. 将导出的 `map_annotations.geojson` 导入 QGIS、ArcGIS 或其他 GIS 软件。

GeoJSON 中每条标注会被转换为一个点要素，坐标为 `[经度, 纬度]`。

## 数据格式说明

### 标注 JSON

标注工具导出的 JSON 大致结构如下：

```json
{
  "version": "0.1",
  "maps": [
    {
      "id": "map_id",
      "title": "map_id",
      "pixelSize": {
        "width": 10000,
        "height": 8000
      },
      "tile": {
        "type": "image"
      },
      "georef": {
        "type": "none"
      }
    }
  ],
  "annotations": [
    {
      "id": "ann_xxxxxxxx",
      "mapId": "map_id",
      "text": "地名",
      "aliases": [],
      "anchor": {
        "x": 1234.56,
        "y": 789.01
      },
      "orientation": "horizontal",
      "style": {
        "fontFamily": "Noto Serif SC",
        "fontSizePxAtScale1": 22,
        "letterSpacing": 1.5,
        "fill": "#000000",
        "stroke": {
          "color": "#ffffff",
          "width": 2
        }
      }
    }
  ]
}
```

其中 `anchor.x` 和 `anchor.y` 是图像像素坐标，不是经纬度。

### TFW

TFW 是 6 行文本参数，用于描述像素坐标到地理坐标的仿射转换关系。当前工具导出的顺序为：

```text
A
D
B
E
C
F
```

在工具内部，对应关系为：

```text
lon = A * x + B * y + C
lat = D * x + E * y + F
```

使用前请确认坐标系为经纬度坐标，例如 WGS84 或 CGCS2000 地理坐标。

### GeoJSON

配准工具导出的 GeoJSON 为标准 `FeatureCollection`：

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [116.391, 39.907]
      },
      "properties": {
        "id": "ann_xxxxxxxx",
        "name": "地名",
        "mapId": "map_id"
      }
    }
  ]
}
```

GeoJSON 坐标顺序为 `[longitude, latitude]`，即 `[经度, 纬度]`。

## GitHub Pages 部署

本项目是纯静态网页，适合直接部署到 GitHub Pages。

部署步骤：

1. 在 GitHub 新建一个仓库。
2. 将本目录下的全部文件上传到仓库根目录。
3. 确认仓库根目录中存在 `index.html`。
4. 进入仓库 `Settings` -> `Pages`。
5. 在 `Build and deployment` 中选择 `Deploy from a branch`。
6. 分支选择 `main`，目录选择 `/root` 或 `/`。
7. 保存设置。
8. 等待 GitHub Pages 构建完成。
9. 访问 GitHub 提供的 Pages 地址。

如果仓库名为 `historical-map-digitization`，你的页面地址通常类似：

```text
https://你的用户名.github.io/historical-map-digitization/
```

## 本地使用

直接用浏览器打开 `index.html` 即可使用。

如果浏览器对本地文件访问有限制，建议启动一个本地静态服务器，例如：

```bash
python -m http.server 8000
```

然后访问：

```text
http://localhost:8000/
```

## 依赖

项目通过 CDN 加载以下前端库：

- OpenSeadragon：超大图像浏览与瓦片渲染。
- GeoTIFF.js：GeoTIFF 读取和解析。
- Leaflet：现代地图底图与控制点选择。
- Fuse.js：标注内容模糊检索。

部署后需要确保访问环境能正常连接这些 CDN。

## 注意事项

- 本项目默认在浏览器本地处理图像，不主动上传用户选择的本地图像。
- 云端地图库功能依赖代码中配置的对象存储地址和跨域访问权限。
- 配准精度取决于控制点数量、控制点分布和地图本身变形情况。
- 控制点应尽量均匀分布在整张地图上，避免集中在局部区域。
- 至少需要 3 个有效控制点才能解算一阶仿射变换。
- 如果历史地图存在明显局部变形，仅靠一阶仿射变换可能无法完全对齐。
- 当前 GeoTIFF 导出功能仍是实验性实现，建议优先使用 TFW、控制点文件和 GeoJSON 进入专业 GIS 流程。

## 适用场景

- 历史地理研究中的旧地图地名提取。
- 方志地图、舆图、旧版地形图的结构化整理。
- 早期遥感图像或扫描图件的轻量级配准。
- 文史研究者、地理信息初学者和教学场景中的在线演示。

## 许可证

如需开源发布，建议在仓库中补充明确的开源许可证文件，例如 MIT License、Apache License 2.0 或 CC BY 4.0。若地图图像、底图或标注数据包含第三方版权内容，请单独确认授权范围。
