# 历史地图数字化工具集

这是一个纯前端静态网站，可直接部署到 GitHub Pages。

## 页面

- `index.html`：首页入口
- `historical_map_annotator_compact_page.html`：历史地图标注工具
- `Georeferencing_page.html`：在线地理配准工具

## GitHub Pages 部署

1. 在 GitHub 新建一个仓库。
2. 上传本目录下的全部文件到仓库根目录。
3. 进入仓库 `Settings` -> `Pages`。
4. `Build and deployment` 选择 `Deploy from a branch`。
5. 分支选择 `main`，目录选择 `/root`，保存。
6. 等待 Pages 构建完成后访问 GitHub 提供的网址。

## 注意

工具依赖 OpenSeadragon、GeoTIFF.js、Leaflet、Fuse.js 等 CDN 资源，线上访问时需要网络能正常访问这些 CDN。
