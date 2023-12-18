# Something about how to improve performance that I learned from MyFiles and other projects :)
## 🚀 Introduction
I think after 5 years worked in MyFiles team of SRV, I have learned more tricks about improve performance (mainly in Android development). So, I want to write it down not only save as a note for myself, but also I want to share with you these. Maybe, something you have known, or not. But I think you can pleasant, review and do not hesitate to give me some suggestions to make this repo really meaningfully. And one thing :) This repo was writtern by myself, when I got a strange feeling status after drinking more last night. Last night, my team have a self-celebrate year end party with current & old member. It's more happy and memorable. So, if you see this repo quite messy (I think it 😆), please do not blame me. Wish you happy when reading it, and help more overview before improve performance for anything. 😂

> _**Note**_:
> - Mostly in this repo, mainly related to improve performance for load and retrieve data. The related part about improve performance in UI such as `sluggish`, `ANR` issues, etc. or related to others part like `unit test` and `UI test`, I will update it soon after learned and refered more about it.
> - One more thing, this repo does not include some data structures and algorithms (DSA) to maximum improve performance such as Trie, TreeSet, Disjoint Set, etc. Since these are method for only and only MyFiles, mean that it was using. I will provide these above DSA in later repo (or not, depends on my mood 😂).

## 📖 Details
✔️ Improve delete from Media Provider
- Divider number of delete operation for each batch when call applyBatch().
- Using set IN of `id` instead compare by `name` column.

✔️ Optimize entry performance when open Analyze Storage first time
- Move heavy task to background (`Dispatchers.IO` in viewModelScope).
- Show loading view when executing heavy task.
- After heavy task execute completed, turn off loading view and show load result.

✔️ Improve coroutine job get many bitmaps from Watch
- Separate get one bitmap logic to a launch/async scope (load simultaneously).
- Using `joinAll()` to join all job.

✔️ Check had any children in a directory
- Instead using API list(), let using getDirectoryStream() of directory.
- Check has any files by using directoryStream.iterator.hasNext().

✔️ Quickly create/mapping new list object
- Using parallelStream() instead forEach() in list.
- If has more than two collector, add it to a supplier object, flatMap() it or not, and using parallelStream() for list.

✔️ Load files from Media Provider
- Always add necessary field to `projection` when query, instead put null value. It will be get all column (include unnecessary column).
- Prioritize attach `sortQuery` to query string, instead sort result list after query completed.

✔️ Check and verify checked items
- If you need to verify new name has existed or not before, using a HashSet to save all existed name. This will always be verified with `O(1)` complexity.
- If you want to verify any item in `list A` has contained in `list B`, please convert `list B` to HashSet with `id` in `Long` type, and you can easily check by API `contains()`.
- Using `HashMap` when you want to perform `verify` and `add` operations at the same time.
- Prioritize using `id` instead `name` when compare.

✔️ Compress and decompress files
- Compress from cloud: open new input stream and read data without download to local.
- Increase buffer size from 4KB to 64KB in order to improve compress/decompress time.
- Using cache helper `ConcurrentHashMap` to save instance of `ZipFile` for avoid create two instances when `open preview` and `decompress from preview`.

✔️ Prevent spam calls to repository
- Using `StateFlow` and `collectLastest()`, and then execute last emitted element, instead execute after each click.

✔️ Using `StringBuilder` to append/insert string instead using `plus` operator to concat string

✔️ Using `SparseArray` to replace `HashMap` if key is integer and `increasing`
- Using `append()` instead `put`.
- `append` is the method that `puts a key/value pair into the array, optimizing for the case where the key is greater than all existing keys in the array`. So, change order to optimize. Example: if the order of keys: 1, 2, 3, 4 => just add to end of sparse array (in O(1)). If the keys aren't increase order. ex: 1, 5, 2, 4, 3 => add to sparse array in O(N) (it's like insert to index).

✔️ Using `SparseArray` with correct type instead primitive type to better performance
- Such as using `SparseBooleanArray` instead `SparseArray`. It does not need to check with object `Boolean`.

✔️ Using `EnumMap` to replace `HashMap` if map of objects is `Enum` class type
- Same behavior like `HashMap`.
- Using enum as key, does not call `hashCode`, so there is no collision happens.

✔️ Check shareable of many files
- Using `ThreadPool` with max core processors. Each core has responsibility to process a sub list from original list (`list size / max thread cores`).
- Using list `Future` to encapsulate this result and publish final result.

✔️ Load thumbnail/detail of many files
- Create an array of `HandlerThread` with ready `start()`.
- Put an async load thumbnail info to each `HandlerThread` item in array and execute it.
- Reloop from index 0 and continue to increase if index is exceeding length of array.
- On `handleMessage`, using `UIHandlerThread`, get `info` (result after executed by `HandlerThread` above) by `fileId` from `UIWorkMap` and update it to main thread.
- Max available thread was calculated by formula `Runtime.getRuntime().availableProcessors() / 2`.

✔️ Replace `file.isHidden()` to `file.getName().charAt(0)` to avoid converting cursor when searching

✔️ Calculate number records from cursor
- Attach projection `COUNT(_id_)` and `cursor.getInt(0)`, instead attach null project and `cursor.getCount()`.

✔️ Define number items to constructor of collection such as ArrayList, HashMap to avoid growing size during add item.
- When convert a collection to other collection, and you know correct size of first collection.

✔️ Get file with condition about mime type
- Instead get all mime types from all files, you should use loop to check mime type of each file and put to `HashSet` in order to distinct this list.
- Loop all of mime types in `HashSet` instead all of duplicated mime types, and append it for query clause.

✔️ Remove `Log` in loop
- Remove log in loop can be reduce performance when iterating inside a loop.

✔️ Prevent using `file.exist()` because it's very expensive call
- Can use it after do operation like as `renameTo`, instead check as a pre-action.

✔️ Reduce unnecessary call get data in loop
- Prepare get list or large data variable outside loop, and use it, instead get it on each step of loop.

✔️ Add key/params for refresh or sync data when you need, not always
- Avoid call many request to Repository/DAO: using one call to insert/delete a list, instead call each request for an insert/delete command (if posible).

✔️ Sync and update file database only when Media Provider change
- Save preference key for last sync time and check for next time.
- Media Provider changed when has `count of id` and `sum of id` changed when compare to previous saved value.
- This above way is deprecated, please using `MediaStore.getGeneration()` to get last generation changed.

✔️ Cache cursor when query if raw record is large than `MAX_LIMIT_RAW` (10000 ~ 20000)
- Check if `cursor.getCount()` is large than `MAX_LIMIT_RAW`.
- If this value is large, please check `MAX_WINDOW_SIZE` - this is number of elements was combine by row and column index.
- If this window size is equals or higher than `MAX_WINDOW_SIZE`, please using a `MatrixCursor` to clone it and save it to a cache cursor.
- Using it for call later. And if possible, please using a SparseArray of `FileInfoConverter` with `cursor` as a param to convert file from cache if you still performs search on a lifecycle.

✔️ Using `CancellationSignal` when query from `ContentResolver`
- Cancel previous query if user continues make new query.
- If you have new request query, please check previous cancellation signal is existed or not => If existed, `cancel()` it and set to `null`. Then, create new `CancellationSignal` for new request.
- If you have many session, please using SparseArray `cancellationSignalMap` with `sessionId` to manage it and cancel corresponding.

✔️ Separate to more item if necessary, such as separate single `RecyclerAdapter` to more `RecyclerAdapter` for proper purpose.

✔️ Check `currentVersion` in local is different with `version` from server before update or load.
- Using a intermediate variable, it prevents unnecessary call to server to check version if you are in a session.

✔️ Using `AtomicBoolean` to check and compare a task is during execute, before execute other task
- Using `compareAndSet` to check, hold and set.

✔️ Using `REGEX` instead `LIKE` when search file extension
- Instead using `LIKE` operator (example: `_display_name LIKE '%.HTML' OR _display_name LIKE '%.PDF'`), please using `(_display_name REGEXP ('(?i).*\.(%s)$'))`.
- `%s` in below query is set of file extensions that you need, you can collect it by using `stream` operator for list extensions and `join` it to a `String`.

✔️ Avoid call many request to Media Provider only retrieve some value like `SUM`, `COUNT`, etc.
- You should request only one query, and select your return values you need under a `cursor`, and then collect it.

✔️ Cache data if possible, avoid query again many times

✔️ Remove call of unnecessary DAO on post execute after delete
- Using flag to force update `LocalFolderChangedDao` and directly access to `Downloads` folder, instead using `DownloadFileInfoDao`.

✔️ Using `static` variable to init cache, and only init it once to avoid bind more time

✔️ Apply `RecycledViewPool` to recycle view holder in `RecyclerView` and improve performance
- _Learn more about it_

✔️ Remove `itemAnimator` to improve performnance in `RecyclerView`
- _Learn more about it_

✔️ Re-create `uniqueId` for trash folder after clear trash files
- After clear trash file, folder of trash is not existed. And next time, user execute move to trash operation, it will generate new `uniqueId` => It takes a bit more time.
- So, please update and regenerate a new `uniqueId` after clear trash file. On next time, if user execute new move to trash operation, it has no taken time to generate `uniqueId`. It will be retrieved from `SharedPreferences`.

✔️ Using `decodeRegion` for `PNG` instead `decodeFileDescriptor`
- `decodeRegion` is good when you create thumbnail for `PNG`, since it reduce load time and total usage memory.
- `decodeFileDescriptor` is good for `JPG`, `JPEG`, instead `decodeRegion`.

✔️ Move `null-check` to outside the `loop`
- If possible, please move `null-check` to outside the `loop`. It can be `repository` or anything which is not always check null.

✔️ Lazy loading for layout
- Using `AsyncLayoutInflater` (_Learn more about it_).
- Using `ViewStub` to inflate view whenever you want or by corresponding condition, instead always appears in your layout.
- Lazy set visibility for unnecessary view, it usually is not essential view and can be shown after main view was appeared.
- Using keyword `by lazy` when create any view. It will be created on the first time called.

✔️ Check and cancel previous task if you request new task
- It has same behavior with the way you use `StateFlow` to prevent spam clicks, but it should be understood that you need a `Atomic` variable to check if current task is loading or not, or `loadSession`, or `loadExecutionId`, or anything you found to define the previous task is executing.
- If previous task is running, return `false` to cancel display new result from new task.

✔️ Pre-initialization some huge task in `Application` class
- Some huge task can mention that `FileGenerator` (generator file info set), `PageStore` (save page type, related class and `id` of fragment will be replaced), `Repository` (repository data for each type and each screen page), `Resources` (contains thumbnail, dummy file, reference to resources, etc.) and some pre-init such as `app folder`, `feature variables`, `configurations`, etc.

✔️ Do not update with obvious views
- Some view likes `Internal Storage` in folder tree, it always has child item, so do not need check sub item count for this. Just only check is mounted or not.

✔️ Restrict declaration and initialization in constructor
- Some `repository` you do not need use it immediately upon initializing the object. Instead, you can use it as needed or lazily initialize it through the interface which is maintained by a `RepositoryHolder`.
- For example, if you pass 2 `repositories` via `AsyncLoadViewInfoManager`, it has long time to initialize these 2 repositories. Instead, create a `RepositoryHolder` class with holding `AsyncLoadViewRepositoryInterface` with 2 methods to get `repository` from this manager class. But, do not forget to check existed and synchronized it to avoid create many times and conflict.

✔️ Increase buffer size to transfer more data volume and reduce number of system calls
- Increase buffer size from 4KB to 64KB or 2MB for `buffer array`. It makes call `read(buffer)` from InputStream faster.
- However, proper buffer size was defined by system. If you increase buffer size without system consultation, you probably will not get the results you expected. But, as I research, it recommends using `2 - 8KB` when the data is accessed from network, or `32 - 64KB` when it's from hard drive.

✔️ Using `groupBy` and check `size of group` to avoid put invalid group to result map
- If you are handling the result of duplicated files, with group has size is lower than 2 items (can not duplicated), please using `groupBy` when stream list and then using `forEach` to check size of group before put to result map.
- This operator proved effective when reduce redundant check predicate.

✔️ Change method to calculate size inside Downloads folder
- Instead using `listFiles()` API and recursive to calculate size of this folder, please using `external.db` provided by `MediaProvider` and query from it.
- First, please check `mime_type IS NOT NULL` and `_data LIKE ?`.
- In `?`, please attach your folder like that: `fullPath + File.separatorChar + '%'` to query all files inside this folder.
- Using `ContentResolver` for query this uri `MediaStore.Files.getContentUri(MediaStore.VOLUME_EXTERNAL)`, and then add selection `"SUM(" + MediaStore.Files.FileColumns.SIZE + ')'`. The long value you get from column index 0 is total size of this folder you need.

✔️ Update necessary item if only it has changed
- Instead request update all recommend cards after uninstalled any app, you can only update `app` cards and update it to `RecyclerView` by `notifyItemChanged`.
- Notice that do not forget remove old `app` card before insert and update new card.
- To more improve, if old card is not existed in `RecyclerView`, please skip to update for it.

✔️ _updating_
