## group
for group hover
```
<div>
  <div class="blue group w-full bg-amber-50 [&>span]:text-green-400">
    <div class="text-black group-[.blue]:text-blue-300 group-hover:group-[.red]:text-red-300">hello</div>
    <span>span</span>
    <div>test</div>
  </div>
</div>
```


## svg
child element styles for svg
```
[&>svg>path:nth-child(3)]:fill-black
```
svg path
```
<a class="[&>svg>path:nth-child(1)]:fill-amber-600 [&>svg>path:nth-child(2)]:fill-green-600">
  <svg width="200" height="200" viewBox="0 0 200 200">
  <!-- First path: a red triangle -->
  <path d="M 10 10 L 190 10 L 100 190 Z" fill="red" />

  <!-- Second path: a blue circle -->
  <path d="M 100 50 A 50 50 0 1 1 100 150 A 50 50 0 1 1 100 50 Z" fill="blue" />

  <!-- Third path: a green line -->
  <path d="M 20 180 L 180 20" stroke="green" stroke-width="5" />
</svg>
</a>
```
list item
```
<div class="[&>ul>li]:last-of-type:text-red-500!">
  <ul>
    <li>1</li>
    <li style="color:aqua">2</li>
    <ul>
</div>
```
