
# Important caveat

 While primitive data types like strings and integers are immutable in Dart,
 collections like lists and maps are mutable. If you directly modify a list
 or map that is provided through a StateProvider, Riverpod may not detect
 the change and consequently won't trigger a rebuild of the widgets'
 ' that depend on that state.
 
To properly update the state provided by a StateProvider when using mutable
 data types, you should create a new instance of the collection whenever you
 want to make changes. This can be done using the spread operator (...), the
 toList() or toMap() methods, or by using constructors like List.from() or Map.from().

*notice line with* final updatedList = List<int>.from(list); // Create a new instance of the list

# Examples

## The names StateProvider and StateNotifierProvider are somewhat misnomers in my opinion,
## They both are notifiers, StateProvider is used for single variables while StateNotifierProvider
## can have methods associated with it to centralize / consolidate logic

# EXAMPLE OF USING StateProvider
## used for simple variables
```
final myTListProvider = StateProvider<List<int>>((ref) => []);
// type annotation is optional
final myListProvider = StateProvider((ref) => []);
              
     // read the list
               final userList = ref.watch(userListProvider);
    // Updating the list with StateProvider using built in class methods
    // and adhering to the caveat above
          void updateList(ProviderContainer container) {
            final list = ref.read(myListProvider.notifier).state;
            final updatedList = List<int>.from(list); // Create a new instance of the list
            updatedList.add(42); // Modify the new list
            container.read(myListProvider.notifier).state = updatedList; // Update the state
    }
    
```

 # EXAMPLE OF USING StateNotifierProvider
``` 
final pmProv = StateNotifierProvider<PMNotifier, List<Map>>((ref) {
   return PMNotifier();
 });

 class PMNotifier extends StateNotifier<List<Map>> {
   PMNotifier() : super([]);

   void addPM(Map pm) {
     state = [...state, pm];
   }
   
     setPM(List npm){
       state = npm;
   }
 } 
    // reading is the same
        ref.watch(pmProv); 
    // updating with StateNotifier method
         ref.read(pmProv.notifier).addPM({"test":"stuff"});
    // don't forget caveat
          List oldList = ref.read(pmProv);
                oldList.add("added to same");
                List newList = List.from(oldList); // new instance
                 // List sameList = oldList // this won't work
                ref.read(pmProv.notifier).setPM(newList);
```

# Listening to vars is the same
```
// use Consumer to localize rebuild
    Consumer(builder: (context, ref, _) {
              final userList = ref.watch(userListProvider);
              
 // or extend StateFull widgets to ConsumerStatefulWidget and
  StateLess widgets to ConsumerWidget with a WidgetRef

//Statefull  (gives implicit access to WidgetRef ref)
 class HomeScreen extends ConsumerStatefulWidget {
  const HomeScreen({Key? key}) : super(key: key);
  @override
  HomeScreenState createState() => HomeScreenState();
}
class HomeScreenState extends ConsumerState<HomeScreen> {
Widget build(context){ ... }

// Stateless (explicit WidgetRef ref)
class DetailsScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {

```
