## baseDao设计思路--接口编程

![](https://raw.githubusercontent.com/Medw1nnn/repo-pics/master/132218255173.jpg)

### 利用泛型编写baseDao接口

```
public interface BaseDao<T> {
    /**
     * 保存实体
     * @param entity
     */
    void save(T entity);
    
    /**
     * 删除实体
     * @param id
     */
    void delete(Long id);
    
    /**
     * 更新实体
     * @param entity
     */
    void update(T entity);
    
    /**
     * 按id查询
     * @param id
     * @return
     */
    T getById(Long id);
    
    /**
     * 按id查询
     * @param ids
     * @return
     */
    List<T> getByIds(Long[] ids);
    
    /**
     * 查询所有
     * @return
     */
    List<T> findAll();
}
```

### 子接口

```

public interface UserDao extends BaseDao<User> {

}
```

### 实现类

```

public class BaseDaoImpl<T> implements BaseDao<T> {
    @Resource
    private SessionFactory sessionFactory;
    private Class<T> clazz;
    
    public BaseDaoImpl(){
        //使用反射技术得到T的真实类型
        //获取当前new的对象的泛型的父类
        ParameterizedType pt =(ParameterizedType) this.getClass().getGenericSuperclass();
        //获取第一个类型参数的真实类型
        this.clazz = (Class<T>) pt.getActualTypeArguments()[0];
        System.out.println("clazz--->"+clazz);
    }
    
    /**
     * 获取当前可用的Session对象
     * @return
     */
    protected Session getSession(){
        return sessionFactory.getCurrentSession();
    }
    
    
    public void delete(Long id) {
        Object obj = getById(id);
        if(obj!=null){
            getSession().delete(obj);
        }
        
    }

    @SuppressWarnings("unchecked")
    public List<T> findAll() {
        return getSession().createQuery(
                "FROM "+clazz.getSimpleName())
                .list();
    }

    @SuppressWarnings("unchecked")
    public T getById(Long id) {
        return (T)getSession().get(clazz, id);
    }

    @SuppressWarnings("unchecked")
    public List<T> getByIds(Long[] ids) {
        return getSession().createQuery(
                "FROM User WHERE id IN (:ids)")
                .setParameterList("ids",ids)
                .list();
    }

    public void save(T entity) {
        getSession().save(entity);
    }

    public void update(T entity) {
        // TODO Auto-generated method stub
        
    }

}
```

### 子接口实现类

```

public class UserDaoImpl extends BaseDaoImpl<User> implements UserDao{

}
```