# Something about how to improve performance that I learned from MyFiles and other projects :)
## 🚀 Introduction
I think after 5 years worked in MyFiles team of SRV, I have learned more tricks about improve performance (mainly in Android development). So, I want to write it down not only save as a note for myself, but also I want to share with you these. Maybe, something you have known, or not. But I think you can pleasant, review and do not hesitate to give me some suggestions to make this repo really meaningfully. And one thing :) This repo was writtern by myself, when I got a strange feeling status after drinking more last night. Last night, my team have a self-celebrate year end party with current & old member. It's more happy and memorable. So, if you see this repo quite messy (I think it 😆), please do not blame me. Wish you happy when reading it. 😂

## 📖 Details
✔️ Improve delete from Media Provider
- Divider number of delete operation for each batch when call applyBatch().
- Using set IN of `id` instead compare by `name` column.

✔️ Optimize entry performance when open Analyze Storage first time
- Move heavy task to background (Dispatchers.IO in viewModelScope).
- Show loading view when executing heavy task.
- After heavy task execute completed, turn off loading view and show load result.

✔️ Improve coroutine job get many bitmaps from Watch
- Separate get one bitmap logic to a launch/async scope (load simultaneously).
- Using joinAll() to join all job.

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

✔️ Using `SparseArray` to replace `HashMap` if key is integer and increasing
- Using `append()` instead `put`.

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

✔️ Replace `file.isHidden()` to `file.getName().charAt(0)` to avoid converting cursor when searching

✔️ Calculate number records from cursor
- Attach projection `COUNT(_id_)` and `cursor.getInt(0)`, instead attach null project and `cursor.getCount()`.

✔️ Define number items to constructor of collection such as ArrayList, HashMap to avoid growing size during add item.
- When convert a collection to other collection, and you know correct size of first collection.

✔️ Get file with condition about mime type
- Instead get all mime types from all files, you should use loop to check mime type of each file and put to `HashSet` in order to distinct this list.
- Loop all of mime types in `HashSet` instead all of duplicated mime types, and append it for query clause.

✔️ Remove `Log` in loop

✔️ Prevent using `file.exist()` because it's very expensive call

✔️ Reduce unnecessary call get data in loop

✔️ Add key/params for refresh or sync data when you need, not always
- Avoid call many request to Repository/DAO: using one call to insert/delete a list, instead call each request for an insert/delete command (if posible).

✔️ Sync and update file database only when Media Provider change
- Save preference key for last sync time and check for next time.
- Media Provider changed when has `count of id` and `sum of id` changed when compare to previous saved value.
- This above way is deprecated, please using `MediaStore.getGeneration()` to get last generation changed.

✔️ _updating_
