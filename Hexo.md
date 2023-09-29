# Hexo使用命令

项目位置：/opt/projects/hexo-sun

## 常用命令

```
hexo g # 生成部署内容
hexo d # 部署到服务器
hexo new shoka xxx笔记 # 使用shoka主题创建新文章
hexo server # 启动本地服务器，生成测试站点
```

## 常用配置

### 部署配置

```yaml
# 站点源地址
url: https://106.14.127.79:8848/
deploy:
  type: 'rsync'
  host: 106.14.127.79
  user: root
  root: /opt/hexo-sun/
  port: 22
  delete: true
  verbose: true
  ignore_errors: true
# 主题设置
theme: shoka
```

## 主题配置

themes/shoka/_config.yml
