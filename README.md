

[![WordPress](https://img.shields.io/badge/WordPress-6.0%2B-blue.svg)](https://wordpress.org/)
[![Bricks Builder](https://img.shields.io/badge/Bricks-1.9.6%2B-orange.svg)](https://bricksbuilder.io/)

Manually maintaining select/radio/checkbox field options for post titles in Bricks forms is time-consuming and error-prone. This solution automatically generates line-separated post titles for your select/dropdown fields.

---

## Solution Code (Add to Child Theme's functions.php)

```
/**
 * Generates line-separated post titles for Bricks select fields
 * 
 * @param string $post_type The post type to retrieve (default: 'post')
 * @return string Line-separated titles or error message
 */
function brx_get_post_titles($post_type = 'post') {
    // Sanitize post type parameter to prevent invalid values
    $post_type = sanitize_key($post_type);
    
    $args = [
        'post_type' => $post_type,
        'posts_per_page' => -1,
        'orderby' => 'title',
        'order' => 'ASC',
        'fields' => 'ids',       
        'no_found_rows' => true  
    ];

    $query = new WP_Query($args);
    $output = '';

    foreach ($query->posts as $post_id) {
        $output .= esc_html(get_the_title($post_id)) . "\n";
    }

    wp_reset_postdata();

    if (empty($output)) {
        return sprintf(
            __('No %s found', 'your-text-domain'),
            esc_html($post_type) 
        );
    }

    return $output;
}
```

---

## Whitelist the function name

Bricks Builder restricts arbitrary PHP execution for **security reasons**. Whitelisting tells Bricks explicitly which functions are safe to execute in your templates.

Add this whitelisting code (typically in `functions.php`):

```php
// Allow our function in Bricks templates
add_filter('bricks/code/echo_function_names', function($allowed_functions) {
    return array_merge((array)$allowed_functions, [
        'brx_get_post_titles' // Whitelist our exact function name
    ]);
});
```

---

## Implementation in Bricks Builder

1. **Basic Usage** (default 'post' post type):
   ```txt
   {echo:brx_get_post_titles()}
   ```

2. **For Different Post Types**:
   ```txt
   {echo:brx_get_post_titles(products)}
   {echo:brx_get_post_titles(service)}
   {echo:brx_get_post_titles(portfolio)}
   ```

3. **In Form Element Settings**:
   - Add to Select field's "Options" input
![CleanShot 2025-01-31 at 2 .16.59|283x500](upload://iPybDNXQZL1AmoChw5Yj0N3PMnY.jpeg)
