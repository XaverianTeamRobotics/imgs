# imgs
Our team image database.

### Rationale
We needed a quick and easy way to host images along with metadata to sort them by.

This repository was designed with programs in mind. All images are stored in `/data/`. To avoid being rate limited, we've stored all data relevant to each image in the file structure itself, allowing you to download every image's metadata with only a few pings to the GitHub API. I guess that would be $`O(1)`$ ping complexity or something?

### Usage
If you're a member of the team looking to upload images to the database, use the [Image Manager](https://robotics.xbhs.net/imgutils).

If you're looking to use this database in your app, follow these instructions:
1. Fetch this repository via the GitHub API.
2. Fetch the latest commit via the GitHub API, using the repository information you just fetched.
3. Fetch the current tree of `/` via the GitHub API, using the full SHA hash of the commit you just fetched.
4. Filter out all other paths besides the `/data` path.
5. Recursively fetch the tree of `/data` using the full SHA hash of the `/data` path.
6. Convert the tree's paths into a tree so it's represented like a directory tree (the Tree API returns an array of directories instead of a directory tree, even though its literally called the Tree API 😒).
7. Using the directory tree you just made, put the data into a more usable structure:
   - At the top level of the tree, you should have a list of directories. These directories are named by the UNIX timestamp at when they were created, so they are sorted in order.
   - Inside these directories, the image's filename is stored in the filename of the `<filename>.file.dbe` file.
     - For example, given the file `image.png.file.dbe`, you know that the image created at this timestamp is called `image.png`.
   - The aspect ratio, either `horizontal`, `vertical`, or `square`, is stored in `<ratio>.ar.dbe`.
     - For example, given the file `vertical.ar.dbe`, you know that the image created at this timestamp has some vertical aspect ratio.
   - Each image in the database can be tagged. Each tag is stored in the filename `<tag>.tag.dbe`.
     - For example, given the file `states.tag.dbe`, you know that the image created at this timestamp has the tag `states`.
   - Descriptions are stored inside the `description.dbe` file because they can be long. If you need the content of the description you will have to ping GitHub for it, however this isn't a big issue because you can simply ping the [GitHub CDN](https://raw.githubusercontent.com/) for it's content instead, which doesn't impose strict rate limits.
   - Using this information, you can come up with a data structure that models this data in the best way for your use case. We recommend modeling it as follows:
```json
{
  "data": // This array is sorted by epoch
  [
    "epoch": "<UNIX epoch at creation time>",
    "image": "<Image filename>",
    "ar": "<Aspect ratio>",
    "tags": [ "<Tag 1>", "<Tag 2>", "<Tag 3>", "<Tag n>" ],
    "description", "<Link to description on https://raw.githubusercontent.com/>"
  ]
}
```


[![XaverianTeamRobotics/imgs © 2023 by Xaverian Brothers High School is licensed under CC BY-NC-ND 4.0](https://licensebuttons.net/l/by-nc-nd/4.0/80x15.png)](./LICENSE)
