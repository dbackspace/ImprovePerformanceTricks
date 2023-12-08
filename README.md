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
