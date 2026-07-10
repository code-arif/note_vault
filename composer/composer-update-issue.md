# Composer — Fooling Composer into updating Composer

### Purpose
Many times composer does not want to update due to php version mismatch. Then composer can be fooled by adding this code in the config object of composer.json file.

---

### Script

```JSON
"config": {
    "optimize-autoloader": true,
    "preferred-install": "dist",
    "sort-packages": true,
    "allow-plugins": {
        "pestphp/pest-plugin": true,
        "php-http/discovery": true,
        "wikimedia/composer-merge-plugin": true
    },
    "platform": {
        "php": "8.3.12"
    }
},

```