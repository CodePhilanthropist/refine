---
id: add-edit-page
title: 2. Adding Edit Page
tutorial:
    order: 0
    prev: tutorial/adding-crud-pages/{preferredUI}/index
    next: tutorial/adding-crud-pages/{preferredUI}/add-show-page
---

## Creating Edit Page

First, we need to create our file, named `edit.tsx`, under the `src/pages/blog-posts` folder. We will then copy the edit page code generated by Inferencer and paste it into the file; for this, follow these steps:

1. Navigate to <a href="http://localhost:3000/blog-posts" rel="noopener noreferrer nofollow">localhost:3000/blog-posts</a> in your browser.

2. Click on any of the "Edit" buttons in the "Actions" column of the table to open the edit page.

3. Click on the "Show Code" button in the bottom right corner of the page.

4. You can see the edit page code generated by Inferencer. Copy it by clicking on the "Copy" button.

5. Paste the code into the newly created `edit.tsx` file.

You can see an example edit page generated by Inferencer below:

```tsx live previewOnly previewHeight=600px url=http://localhost:3000/blog-posts/edit/123
setInitialRoutes(["/blog-posts/edit/123"]);

import { Refine } from "@refinedev/core";
import { HeadlessInferencer } from "@refinedev/inferencer/headless";
import routerBindings, {
    NavigateToResource,
    UnsavedChangesNotifier,
} from "@refinedev/react-router-v6";
import dataProvider from "@refinedev/simple-rest";

import { BrowserRouter, Route, Routes } from "react-router-dom";

const App = () => {
    return (
        <BrowserRouter>
            <Refine
                routerProvider={routerBindings}
                dataProvider={dataProvider("https://api.fake-rest.refine.dev")}
                resources={[
                    {
                        name: "blog_posts",
                        list: "/blog-posts",
                        show: "/blog-posts/show/:id",
                        create: "/blog-posts/create",
                        edit: "/blog-posts/edit/:id",
                    },
                ]}
                options={{
                    syncWithLocation: true,
                    warnWhenUnsavedChanges: true,
                }}
            >
                <Routes>
                    <Route
                        index
                        element={<NavigateToResource resource="blog_posts" />}
                    />

                    <Route path="/blog-posts">
                        <Route index element={<HeadlessInferencer />} />
                        <Route
                            path="show/:id"
                            element={<HeadlessInferencer />}
                        />
                        <Route
                            path="edit/:id"
                            element={<HeadlessInferencer />}
                        />
                        <Route path="create" element={<HeadlessInferencer />} />
                    </Route>

                    <Route path="*" element={<div>Error!</div>} />
                </Routes>

                <UnsavedChangesNotifier />
            </Refine>
        </BrowserRouter>
    );
};

render(<App />);
```

## Understanding the Edit Component

### Hooks and Components in Edit Page

-   The `useForm` hook is imported from `@refinedev/react-hook-form`, which combines the features of `useForm` hook from both **React Hook Form** and `@refinedev/core`. When used the in the edit page, it fetches record data with the URL's `id`, populating and submitting the form with `dataProvider`'s `update` method. It also offers `saveButtonProps` for the form's submit button.

    > For more information, refer to the [`useForm` documentation](/docs/packages/documentation/react-hook-form/useForm/) and the [**React Hook Form** documentation&#8594](https://react-hook-form.com/)

-   The `useNavigation` hook is used for navigating between pages. In this case, we are using it to navigate to the `list` pages when the user clicks on the "Blog Posts List" buttons.

    > For more information, refer to the [`useNavigation` documentation &#8594](/docs/api-reference/core/hooks/navigation/useNavigation/)

### Handling Relationships

On the edit page, we may need to select a record from another resource.

For example, if we need to select a category from the `categories` resource to assign the blog post to the category, we can use the `useSelect` hook provided by **refine**. This hook fetches the data by passing the params to the `dataProvider`'s `getList` method and then returns the `options` to be used in the `<select/>` component.

> For more information, refer to the [`useSelect` documentation&#8594](/docs/api-reference/core/hooks/useSelect/)

In the auto-generated edit page code, Inferencer used the `useSelect` hook to select a category from the `categories` resource like below:

```tsx
const { options: categoryOptions } = useSelect({
    resource: "categories",
});
```

The `useSelect` hook returns 10 records by default, but the category of the blog post may not be in the first 10 records. To solve this problem, we can use the `defaultValue` prop to set the default value of the `useSelect` hook like below:

```tsx
const { options: categoryOptions } = useSelect({
    resource: "categories",
    defaultValue: blogPostsData?.category?.id,
});
```

## Adding the Edit Page to the App

Now that we have created the edit page, we can add it to the `App.tsx` file:

1. Open `src/App.tsx` file on your editor.

2. Import the created `BlogPostEdit` component.

3. Replace the `HeadlessInferencer` component with the `BlogPostEdit` component.

```tsx title="src/App.tsx"
import { Refine } from "@refinedev/core";
import { HeadlessInferencer } from "@refinedev/inferencer/headless";
import routerBindings, {
    NavigateToResource,
    //highlight-next-line
    UnsavedChangesNotifier,
} from "@refinedev/react-router-v6";
import dataProvider from "@refinedev/simple-rest";
import { BrowserRouter, Route, Routes } from "react-router-dom";

import { BlogPostList } from "pages/blog-posts/list";
//highlight-next-line
import { BlogPostEdit } from "pages/blog-posts/edit";

const App = () => {
    return (
        <BrowserRouter>
            <Refine
                routerProvider={routerBindings}
                dataProvider={dataProvider("https://api.fake-rest.refine.dev")}
                resources={[
                    {
                        name: "blog_posts",
                        list: "/blog-posts",
                        show: "/blog-posts/show/:id",
                        create: "/blog-posts/create",
                        // highlight-next-line
                        edit: "/blog-posts/edit/:id",
                    },
                ]}
                // highlight-start
                options={{
                    syncWithLocation: true,
                    warnWhenUnsavedChanges: true,
                }}
                // highlight-end
            >
                <Routes>
                    <Route
                        index
                        element={<NavigateToResource resource="blog_posts" />}
                    />

                    <Route path="blog-posts">
                        <Route index element={<BlogPostList />} />
                        <Route
                            path="show/:id"
                            element={<HeadlessInferencer />}
                        />
                        {/* highlight-next-line */}
                        <Route path="edit/:id" element={<BlogPostEdit />} />
                        <Route path="create" element={<HeadlessInferencer />} />
                    </Route>

                    <Route path="*" element={<div>Error!</div>} />
                </Routes>
                {/* highlight-next-line */}
                <UnsavedChangesNotifier />
            </Refine>
        </BrowserRouter>
    );
};
export default App;
```

Now, we can see the edit page in the browser at <a href="http://localhost:3000/blog-posts/edit/123" rel="noopener noreferrer nofollow">localhost:3000/blog-posts/edit/123</a>

<br/>

<Checklist>

<ChecklistItem id="add-edit-page-headless">
I have added the edit page to the app.
</ChecklistItem>
<ChecklistItem id="add-edit-page-headless-2">
I understood the edit page components and hooks.
</ChecklistItem>
<ChecklistItem id="add-edit-page-headless-3">
I understood the relationship handling.
</ChecklistItem>

</Checklist>
