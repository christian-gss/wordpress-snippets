# Wordpress Snippets
Author: Christian Marquez

### Limit text
This code will limit text and add `...` in the end.
```
function truncate($text, $chars = 50) {
    if(strlen($text) > $chars) {
        $text = $text.' ';
        $text = substr($text, 0, $chars);
        $text = substr($text, 0, strrpos($text ,' '));
        $text = $text.'...';
    }
    return $text;
}
```
You can now use 
```
$truncTitle = truncate(get_the_title(), 40);
echo $title;
```
### Manual Pagination
Add this to your `functions.php`, this will only works with [The Loop](https://codex.wordpress.org/The_Loop).
```
function pagination($pages = '', $range = 1) {
  $showitems = ($range * 2)+1;
  global $paged;
  if(empty($paged)) $paged = 1;
  if($pages == '') {
    global $wp_query;
    $pages = $wp_query->max_num_pages;
    if(!$pages) {
      $pages = 1;
    }
  }
  if(1 != $pages) {
    echo "<div class=\"pagination__number pagination__number--voice\"><div class=\"pagination__numberLists\"><span class=\"page-of\">PAGE ".$paged." of ".$pages."</span>";
    if($paged > 2 && $paged > $range+1 && $showitems < $pages) echo "<a href='".get_pagenum_link(1)."' class=\"page-link\">&laquo;</a>";
    if($paged > 1 && $showitems < $pages) echo "<a href='".get_pagenum_link($paged - 1)."' class=\"page-link\">&lsaquo;</a>";
    for ($i=1; $i <= $pages; $i++) {
      if (1 != $pages &&( !($i >= $paged+$range+1 || $i <= $paged-$range-1) || $pages <= $showitems )) {
        echo ($paged == $i)? "<span class=\"page-link current\">".$i."</span>":"<a href='".get_pagenum_link($i)."' class=\"page-link inactive\">".$i."</a>";
      }
    }
    if ($paged < $pages && $showitems < $pages) echo "<a href=\"".get_pagenum_link($paged + 1)."\" class=\"page-link\">&rsaquo;</a>";
    if ($paged < $pages-1 &&  $paged+$range-1 < $pages && $showitems < $pages) echo "<a href='".get_pagenum_link($pages)."' class=\"page-link\">&raquo;</a>";
    echo "</div></div>\n";
  }
}
```
You can now add this after your loop.
```
if (function_exists("pagination")) {
  pagination($blog->max_num_pages);
}
```
Add this to your query 
```
$paged = ( get_query_var( 'paged' ) ) ? get_query_var( 'paged' ) : 1;
```

You can now add the `$paged` to the argument `paged`.
```
$args = array(
    'offset' => $offset,
    'post_type' => 'post',
    'posts_per_page' => $limit,
    'paged' => $paged
);
```
### Return Content of a particular page by ID
You can return page content of a page by ID using this approach.
```
function get_the_content_by_id($post_id) {
  $page_data = get_page($post_id);
  if ($page_data) {
    return $page_data->post_content;
  }
  else return false;
}
```
### Basic Loop
Basic loop with args.
```
<?php 
$args = array(
    'post_type' => 'post',
    'posts_per_page' => 5,
);
$query = new WP_Query( $args );

if ( $blog->have_posts() ) : while ( $blog->have_posts() ) : $blog->the_post();
    $categories = get_the_category($post->ID);
    $cat_link = get_category_link($categories[0]->cat_ID);

    // To get category name and link
    <a href="<?php echo $cat_link; ?>"><?php echo $categories[0]->cat_name; ?></a>

    // Post title
    <a href="<?php the_permalink(); ?>"<?php echo get_the_title(); ?></a>

    // Get Date
    <p><?php echo get_the_date('Y/m/d');?></p>

endwhile; 
else : endif;
?>
```
### Show all categories
This code display all categories
```
$categories = get_categories();
foreach($categories as $category) { 
    echo '<li><a href="' . get_category_link($category->term_id) . '">' . $category->name . '</a></li>';
}
```
