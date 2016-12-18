##Annotation

###@Cache

    public @interface Cache {

        /**
         * 缓存的过期时间，单位：秒，如果为0则表示永久缓存
         * @return 时间
         */
        int expire();
        /**
         * 动态获取缓存过期时间的表达式
         * @return 时间
         */
        String expireExpression() default "";

        /**
         * 预警自动刷新时间(单位：秒)，必须满足 0 < alarmTime < expire才有效
         * @return 时间
         */
        int alarmTime() default 0;
        /**
         * 自定义缓存Key，支持表达式
         * @return String 自定义缓存Key
         */
        String key() default "";

        /**
         * 设置哈希表中的字段，如果设置此项，则用哈希表进行存储，支持表达式
         * @return String
         */
        String hfield() default "";

        /**
         * 是否启用自动加载缓存， 缓存时间必须大于120秒时才有效
         * @return boolean
         */
        boolean autoload() default false;

        /**
         * 自动缓存的条件表达式，可以为空，返回 true 或者 false，如果设置了此值，autoload() 就失效，例如：null != #args[0].keyword，当第一个参数的keyword属性为null时设置为自动加载。
         * @return 表达式
         */
        String autoloadCondition() default "";

        /**
         * 当autoload为true时，缓存数据在 requestTimeout 秒之内没有使用了，就不进行自动加载数据,如果requestTimeout为0时，会一直自动加载
         * @return long 请求过期
         */
        long requestTimeout() default 36000L;

        /**
         * 缓存的条件表达式，可以为空，返回 true 或者 false，只有为 true 才进行缓存
         * @return String
         */
        String condition() default "";

        /**
         * 缓存的操作类型：默认是READ_WRITE，先缓存取数据，如果没有数据则从DAO中获取并写入缓存；如果是WRITE则从DAO取完数据后，写入缓存
         * @return CacheOpType
         */
        CacheOpType opType() default CacheOpType.READ_WRITE;

        /**
         * 并发等待时间(毫秒),等待正在DAO中加载数据的线程返回的等待时间。
         * @return 时间
         */
        int waitTimeOut() default 500;
        /**
         * 扩展缓存
         * @return
        */
        ExCache[] exCache() default @ExCache(expire=-1, key="");
    }

###@ExCache

  使用场景举例：如果系统中用getUserById和getUserByName,两种方法来获取用户信息，我们可以在getUserById 时把 getUserByName 的缓存也生成。反过来getUserByName 时，也可以把getUserById 的缓存生成：

    @Cache(expire=600, key="'USER.getUserById'+#args[0]", exCache={@ExCache(expire=600, key="'USER.getUserByName'+#retVal.name")})
    public User getUserById(Long id){... ...}
    
    @Cache(expire=600, key="'USER.getUserByName'+#args[0]", exCache={@ExCache(expire=600, key="'USER.getUserById'+#retVal.id")})
    public User getUserByName(Long id){... ...}
    
    
  @ExCache 详细参数：

    public @interface ExCache {

        /**
         * 缓存的过期时间，单位：秒，如果为0则表示永久缓存
         * @return 时间
         */
        int expire();

        /**
         * 动态获取缓存过期时间的表达式
         * @return 时间
         */
        String expireExpression() default "";

        /**
         * 自定义缓存Key表达式
         * @return String 自定义缓存Key
        */
        String key();

        /**
         * 设置哈希表中的字段表达式，如果设置此项，则用哈希表进行存储
         * @return String
        */
        String hfield() default "";

        /**
         * 缓存的条件表达式，可以为空，返回 true 或者 false，只有为 true 才进行缓存
         * @return String
        */
        String condition() default "";

        /**
         * 通过表达式获取需要缓存的数据，如果没有设置，则默认使用方法返回值
         * @return
        */
        String cacheObject() default "";
 
    }

###@CacheDelete

    public @interface CacheDelete {

        CacheDeleteKey[] value();// 支持删除多个缓存
    }

###@CacheDeleteKey

    public @interface CacheDeleteKey {

        /**
         * 缓存的条件表达式，可以为空，返回 true 或者 false，只有为 true 才进行缓存
         * @return String
         */
        String condition() default "";

        /**
         * 删除缓存的Key表达式。
         * @return String
         */
        String value();

        /**
         * 哈希表中的字段表达式
         * @return String
         */
        String hfield() default "";
    }

