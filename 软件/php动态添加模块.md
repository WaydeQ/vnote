# php动态添加模块

- phpize是用来扩展php扩展模块的，通过phpize可以建立php的外挂模块
```
/application/php/bin/phpize
./configure --with-php-config=/application/php/bin/php-config
make && make install
```