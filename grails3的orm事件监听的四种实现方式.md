# grails3的orm事件监听的四种实现方式

## 四种方式
+ Listening to events from GORM asynchronously
    - 利用 @Subscriber注解
    - 异步的方式，所以不会影响save,update,delete等事件
    - @Subscriber是对所有domain的事件监听，所以需要自己去做是否是某张表的的判断，监听范围比较大。 
    ```$xslt
        package demo
        
        import grails.events.annotation.Subscriber
        import grails.events.annotation.gorm.Listener
        import groovy.transform.CompileStatic
        import groovy.util.logging.Slf4j
        import org.grails.datastore.mapping.engine.event.AbstractPersistenceEvent
        import org.grails.datastore.mapping.engine.event.PostDeleteEvent
        import org.grails.datastore.mapping.engine.event.PostInsertEvent
        import org.grails.datastore.mapping.engine.event.PostUpdateEvent
        
        @Slf4j
        @CompileStatic
        class AuditListenerService {
        
            AuditDataService auditDataService
        
            Long bookId(AbstractPersistenceEvent event) {
                if ( event.entityObject instanceof Book ) {
                    return ((Book) event.entityObject).id 
                }
                null
            }
        
            @Subscriber 
            void afterInsert(PostInsertEvent event) {
                Long bookId = bookId(event)
                if ( bookId ) {
                    log.info 'After book save...'
                    auditDataService.save('Book saved', bookId)
                }
            }
        
            @Subscriber 
            void afterUpdate(PostUpdateEvent event) { 
                Long bookId = bookId(event)
                if ( bookId ) {
                    log.info "After book update..."
                    auditDataService.save('Book updated', bookId)
                }
            }
        
            @Subscriber 
            void afterDelete(PostDeleteEvent event) {
                Long bookId = bookId(event)
                if ( bookId ) {
                    log.info 'After book delete ...'
                    auditDataService.save('Book deleted', bookId)
                }
            }
        }
    ```
+ Listening to events from GORM synchronously
    - 利用 @Listener注解
    - 同步的方式，与上面的相反
    - @Listener注解声明的函数，函数名存在规则，必须是on$DomainName$EventName。这种格式，才能找到对应的domain和事件。所以这个Listener监听的范围就比较小。
    ```$xslt
        import grails.events.annotation.gorm.Listener
        import groovy.transform.CompileStatic
        import org.grails.datastore.mapping.engine.event.AbstractPersistenceEvent
        import org.grails.datastore.mapping.engine.event.PreInsertEvent
        import org.grails.datastore.mapping.engine.event.PreUpdateEvent
        
        @CompileStatic
        class TitleListenerService {
        
            @Listener(Book) 
            void onBookPreInsert(PreInsertEvent event) {
                populateSerialNumber(event)
                populateFriendlyUrl(event)
            }
        
            @Listener(Book) 
            void onBookPreUpdate(PreUpdateEvent event) { 
                Book book = ((Book) event.entityObject)
                if ( book.isDirty('title') ) { 
                    populateFriendlyUrl(event)
                }
            }
        }    
    ```
+ GORM supports the registration of events
    - domain里默认的被注册的事件
    - 同步的方式
    - 在事件的函数，不要做flush session
        + Do not attempt to flush the session within an event (such as with obj.save(flush:true)). Since events are fired during flushing this will cause a StackOverflowError.
    ```$xslt
        class Person {
           private static final Date NULL_DATE = new Date(0)
           String firstName
           String lastName
           Date signupDate = NULL_DATE
        
           def beforeInsert() {
              if (signupDate == NULL_DATE) {
                 signupDate = new Date()
              }
           }
        }
    ```
    + 不止局限于Hibernate 
        -  This API is not tied to Hibernate and also works for other persistence plugins such as the MongoDB plugin for GORM.
    + 可以通过自己实现AbstractPersistenceEventListener 的方式，去指定监听的表和行为
    ```$xslt
        public MyPersistenceListener(final Datastore datastore) {
            super(datastore)
        }
        @Override
        protected void onPersistenceEvent(final AbstractPersistenceEvent event) {
            switch(event.eventType) {
                case PreInsert:
                    println "PRE INSERT ${event.entityObject}"
                break
                case PostInsert:
                    println "POST INSERT ${event.entityObject}"
                break
                case PreUpdate:
                    println "PRE UPDATE ${event.entityObject}"
                break;
                case PostUpdate:
                    println "POST UPDATE ${event.entityObject}"
                break;
                case PreDelete:
                    println "PRE DELETE ${event.entityObject}"
                break;
                case PostDelete:
                    println "POST DELETE ${event.entityObject}"
                break;
                case PreLoad:
                    println "PRE LOAD ${event.entityObject}"
                break;
                case PostLoad:
                    println "POST LOAD ${event.entityObject}"
                break;
            }
        }
        
        @Override
        public boolean supportsEventType(Class<? extends ApplicationEvent> eventType) {
            return true
        }
    ```
    向应用注册
    ```$xslt
        def grailsApplication
        def init = {
            def applicationContext = grailsApplication.mainContext
            applicationContext.eventTriggeringInterceptor.datastores.each { k, datastore ->
                applicationContext.addApplicationListener new MyPersistenceListener(datastore)
            }
        }
        
        //or use this in a plugin:
        
        def doWithApplicationContext = { applicationContext ->
            grailsApplication.mainContext.eventTriggeringInterceptor.datastores.each { k, datastore ->
                applicationContext.addApplicationListener new MyPersistenceListener(datastore)
            }
        }
    ```
+ Hibernate Events
    - Hibernate的事件列表和对应的需要实现的函数名
    
    | Name      | Interface |
    | ----------- | ----------- |
    | auto-flush      | 	AutoFlushEventListener       |
    | merge   | 	MergeEventListener        |
    | create   | 	PersistEventListener        |
    | create-onflush   | 		PersistEventListener        |
    | delete	   | 		DeleteEventListener        |
    | dirty-check   | 		DirtyCheckEventListener        |
    | evict   | 		EvictEventListener        |
    | flush   | 		FlushEventListener        |
    | flush-entity   | 		FlushEntityEventListener        |
    | load   | 	LoadEventListener        |
    | load-collection   | 		InitializeCollectionEventListener        |
    | lock   | 	LockEventListener        |
    | refresh   | 		RefreshEventListener        |
    | replicate   | 		ReplicateEventListener        |
    | save-update   | 		SaveOrUpdateEventListener        |
    | save   | 		SaveOrUpdateEventListener        |
    | update   | 	SaveOrUpdateEventListener        |
    | pre-load   | 		PreLoadEventListener        |
    | pre-update   | 	PreUpdateEventListener        |
    | pre-delete   | 	PreDeleteEventListener        |
    | pre-insert   | 	PreInsertEventListener      |
    | pre-collection-recreate   | PreCollectionRecreateEventListener|
    | pre-collection-remove   | PreCollectionRemoveEventListener  |
    | pre-collection-update   | 	PreCollectionUpdateEventListener   |
    | post-load   | 	PostLoadEventListener        |
    | post-update   | 		PostUpdateEventListener        |
    | post-delete   | 	PostDeleteEventListener        |
    | post-insert   | 	PostInsertEventListener        |
    | post-commit-update   | 	PostUpdateEventListener        |
    | post-commit-delete   | 	PostDeleteEventListener        |
    | post-commit-insert   | 	PostInsertEventListener   |
    | post-collection-recreate   |PostCollectionRecreateEventListener        |
    | post-collection-remove   | PostCollectionRemoveEventListener|
    | post-collection-update |PostCollectionUpdateEventListener|


    
    - 声明监听bean
  ```$xslt 
   beans = {
       auditListener(AuditEventListener)
    
       hibernateEventListeners(HibernateEventListeners) {
          listenerMap = ['post-insert': auditListener,
                         'post-update': auditListener,
                         'post-delete': auditListener]
       }
    }

   ```
