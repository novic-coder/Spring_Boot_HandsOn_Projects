Creation of the user and saving it with data bases
UserRepository userRepository = context.getBean(UserRepository.class);
		// Saving Singel user 
//		User user = new User();
//		user.setName("Ravi Ranjan");
//		user.setAge(25);
//		user.setCity("Asansol");
//		user.setGender("Male");
//		
//		User user1 = userRepository.save(user);
//		System.out.println(user1);


  THE ABOVE CODE WILL BE SAVING ONE USER TO THE DATA BASE 



WHEN WE HAVE MANY USERS 

CREATE LIST OF ALL USERS AND THEN USE ITERABLE TO SAVE IT AND ACCESS IT

User user1 = new User();
		user1.setName("Jayshree Ruje");
		user1.setAge(35);
		user1.setCity("Texas");
		user1.setGender("Female");
		
		User user2 = new User();
		user2.setName("Dolly RUje");
		user2.setAge(52);
		user2.setCity("Asansol");
		user2.setGender("Female");
		
		User user3 = new User();
		user3.setName("Akshay Tolani");
		user3.setAge(39);
		user3.setCity("Texas");
		user3.setGender("Male");
		
		/*
		 * User resultUser = userRepository.save(user1); System.out.println(resultUser);
		 */
		
		List<User> users = List.of(user1,user2,user3);
		Iterable<User> result = userRepository.saveAll(users);
		
		result.forEach(user->{
			System.out.println(user);
		});


  // UPDATE USERS  -- update by user id
		//Find the data -> Set the updated value -> save it
		
		Optional<User> optional = userRepository.findById(52);
		User user = optional.get();
		user.setGender("Female");
		User result = userRepository.save(user);
		System.out.println(result);



// Finding all and iterating through them
		Iterable<User> itr = userRepository.findAll();
		Iterator<User> iterator = itr.iterator();
		while(iterator.hasNext()) {
			User user=iterator.next();
			System.out.println(user);
		}


  // Lambda Function
		
		itr.forEach(user->{
			System.out.println(user);
		});



  // Deleting the user element 
		
		userRepository.deleteById(1);
		System.out.println("deleted");


  //** DELETE BY ITERATING AND DELETING **

  
//		// Deleting the user element 
//		
//		userRepository.deleteById(1);
//		System.out.println("deleted");
		
		
		Iterable<User> itr = userRepository.findAll();
		
		itr.forEach(user->System.out.println(user));
		userRepository.deleteAll(itr);


  LECTURE 14 -- Custom Finder methods

Inside the interface user repository we need to give signature of the methods
-------------------------------------------------------------------------------------
public interface UserRepository extends CrudRepository<User, Integer>{

	public List<User> findByName(String name);
	
	public List<User> findByNameAndCity(String name, String city);
}

-------------------------------------------------------------------------------------------

Add Some users to the data base using Application file
---------------------------------------------------------------------------

User user1 = new User();
		user1.setName("Jayshree Ruje");
		user1.setAge(35);
		user1.setCity("Texas");
		user1.setGender("Female");
		
		User user2 = new User();
		user2.setName("Dolly Ruje");
		user2.setAge(52);
		user2.setCity("Asansol");
		user2.setGender("Female");
		
		User user3 = new User();
		user3.setName("Akshay Tolani");
		user3.setAge(39);
		user3.setCity("Texas");
		user3.setGender("Male");
		
		/*
		 * User resultUser = userRepository.save(user1); System.out.println(resultUser);
		 */
		
		List<User> users = List.of(user1,user2,user3);
		Iterable<User> result = userRepository.saveAll(users);
		
		result.forEach(user->{
			System.out.println(user);
		});

------------------------------------------------------------------------------------------------------------------------

Inside the same file we ill invoke the methods to get the output

// Custom Finder Method
		
		List<User> users1 = userRepository.findByName("Dolly Ruje");
		users1.forEach(e->System.out.println(e));
		
		List<User> user11 = userRepository.findByNameAndCity("Jayshree Ruje", "Texas");
		user11.forEach(e->System.out.println(e));



-------------------------------------------------------------------------------------------------------------------


LECTURE 15
---------------

Writing own Query and firing it to get result
---------------------------------------------

@Query
	JPQL --> Hibernate query languauge
 	Native Query

Inside the JPA repository Interface file 

        @Query("select u FROM User u")
	public List<User> getAllUsers();
	
	@Query("select u FROM User u WHERE u.name=:n and u.city=:c")
	public List<User> getUserByNameAndCity(@Param("n") String name, @Param("c") String city);
	
	@Query(value= "select * from user", nativeQuery = true)
	public List<User> getuser();

 Calling the above method in main class 


              List <User> allUser = userRepository.getAllUsers();
		
		allUser.forEach(user->{
			System.out.println(user);
		});
		
		System.out.println("-------------------------------------------");
		
		List <User> userByName = userRepository.findByNameAndCity("Akshay Tolani", "Texas");
		userByName.forEach(e->System.out.println(e));
		
		System.out.println("-----------------------------------------------");
		
		userRepository.getuser().forEach(e-> System.out.println(e));

  The Above Example is related to the Native Query
  

  
  
