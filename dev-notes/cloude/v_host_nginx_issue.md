```json
index index.html;
location / {
try_files $uri /index.html;
}