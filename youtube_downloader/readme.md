﻿# Service cho phép play mp3, download file mp3 từ link youtube và lưu vào thư mục local trên homeassistant
**Khai báo trong configuration.yaml như sau:**

```python
downloader:
  download_dir: /config/www/media #thư mục local trên homeassistant

youtube_downloader:
```
**Dịch vụ play mp3:**

```python
service: youtube_downloader.play_mp3
  data:
    url: 'link youtube muốn play'
    entity_id: 'entity loa muốn phát'
```

**Dịch vụ download mp3:**

```python
service: youtube_downloader.download_mp3
  data:
    url: 'link youtube muốn download'
    sub_dir: 'music'   #thư mục con muốn lưu trữ trên hass (là subdir của thư mục download_dir khai báo trong file configuration.yaml
```

**Fix lỗi file youtube.py**
```python
'uploader_id': self._search_regex(r'/(?:channel|user)/([^/?&#]+)', owner_profile_url, 'uploader id') if owner_profile_url else None,
  File "/usr/local/lib/python3.10/site-packages/youtube_dl/extractor/common.py", line 1012, in _search_regex
    raise RegexNotFoundError('Unable to extract %s' % _name)
```

Trên docker:
Gõ lệnh sau tìm file youtube.py:
```linux
find / -name youtube.py
```

![image](https://user-images.githubusercontent.com/5145311/222424246-896ab8f1-3d7d-48af-a932-087902c0eedb.png)

Tìm dòng
```python
'uploader_id': self._search_regex(r'/(?:channel|user)/([^/?&#]+)', owner_profile_url, 'uploader id') if owner_profile_url else None,
```

và sửa thành:
```python
'uploader_id': self._search_regex(r'/(?:channel/|user/|(?=@))([^/?&#]+)', owner_profile_url, 'uploader id', default=None),
```

Save file rồi reboot lại homeassistant


**Cách fix tự động:**

Tải file fix_youtube_py.sh về đẩy vào thư mục trên hass. VD: /usr/share/hassio/homeassistant/scripts/fix_youtube_py.sh

Chạy lệnh sau để run script vào 1h sáng hàng ngày:

chmod +x /usr/share/hassio/homeassistant/scripts/fix_youtube_py.sh

crontab -e

Thêm dòng sau vào cuối file và save lại:

0 1 * * * /usr/share/hassio/homeassistant/scripts/fix_youtube_py.sh

Chúc bạn thành công!
