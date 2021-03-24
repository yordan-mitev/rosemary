---
title: Get Started with Kendo UI for Angular
page_title: Get Started with Your Angular Project | Kendo UI for Angular
description: Learn how to setup an Angular project and det started with the Kendo UI components for the Angular framework.
slug: getting_started
use_runner: true
position: 2
toc: true
brand: getting-started
heading: Get Started
---

# Get Started with Kendo UI for Angular

This tutorial describes how to install and start using Kendo UI for Angular.

* First, we will go through the installation steps.
* Next, we'll create a simple app with a few Kendo UI Angular for components. The [app source code](https://github.com/telerik/kendo-angular-getting-started) is available for your reference.
* Finally, you will learn how to activate your Kendo UI for Angular license.
* On every step, we will recommend relevant learning resources that will help you be successful with Kendo UI for Angular.

<blockquote class="alert-note">
    <h3 style="margin-top: 0;">Do You Prefer Video?</h3>
    <p>You can setup an Angular project and add Kendo UI for Angular components by following the steps in this guide. Alternatively, you can check our <a href="https://www.telerik.com/campaigns/kendo-ui/using-kendo-ui-with-angular-video-tutorial">Angular video tutorials</a> or our <a href="https://www.telerik.com/kendo-angular-ui/components/virtual-class/">full-blown Kendo UI Angular video course</a>.</p>
</blockquote>

## Set up the Angular Project

<blockquote class="alert-note">
    The latest Kendo UI for Angular packages target the current <a href="https://angular.io/guide/releases#support-policy-and-schedule">Angular long-term support versions</a> up to the latest official Angular version.
</blockquote>

After checking the [system requrements]({% slug requirements_installation %}), the easiest way to start with Angular is to use the [Angular CLI Tool](https://github.com/angular/angular-cli). To scaffold your project structure, follow the tool's [installation instructions](https://github.com/angular/angular-cli#installation):

```sh
npm install -g @angular/cli
ng new kendo-angular-app
```

The `ng new` command will prompt you for a few settings of the new Angular app. Let's use these:

* *Enforce stricter type checking and bundle budgets?* - **No**
* *Add Angular routing?* - **Yes**
* *Stylesheet format?* - **CSS**

When the new app is generated, change the current directory to `kendo-angular-app`:

```sh
cd kendo-angular-app
```

Now replace the content of `src/app/app.component.html` with: 

```html
<h1>Hello Kendo UI for Angular!</h1>
```

Then, build and open the Angular app in the browser:

```sh
ng serve -o
```

We are ready to dive into Kendo UI for Angular! Next, we will [add some data](#add-data) to our app, and then we will bind this data to the [Kendo DropDownList](#add-a-dropdownlist) and the [Kendo Grid](#add-a-grid) components.

## Add Data

Let's prepare to use professional data-driven UI components by adding some sample data to our app. For simplicity, we will add static local JSON data to our project and a service that you can later modify to use remote data. Create the following three files and copy-paste their content from the linked files in GitHub: 

* [src/app/data.products.ts](https://github.com/telerik/kendo-angular-getting-started/blob/master/src/app/data.products.ts)
* [src/app/data.categories.ts](https://github.com/telerik/kendo-angular-getting-started/blob/master/src/app/data.categories.ts)
* [src/app/product.service.ts](https://github.com/telerik/kendo-angular-getting-started/blob/master/src/app/product.service.ts)

## Add a DropDownList

Let's add a [Kendo UI Angular DropDownList]({% slug overview_ddl %}) to our app and [bind it to an array of objects]({% slug databinding_ddl %}#toc-arrays-of-complex-data).

1. The [package installation]({% slug overview_dropdowns %}) requires a single step:

```sh
ng add @progress/kendo-angular-dropdowns
```

  The `ng add` command will also automatically install the [Default Kendo UI theme]({% slug themesandstyles %}).

2. Open `src/app/app.component.ts` and import `categories` from `data.categories`:

```ts
import { categories } from "./data.categories";
```

3. In the same file, declare the variables that we will use inside the component declaration:

```ts
export class AppComponent {
  public dropDownItems = categories;
  public defaultItem = { text: "Filter by Category", value: null };
}
```

5. Finally, open `src/app/app.component.html` and add the DropDownList markup:

```html
<p>
  <kendo-dropdownlist [data]="dropDownItems" [textField]="'text'" [valueField]="'value'" [defaultItem]="defaultItem">
  </kendo-dropdownlist>
</p>
```

The DropDownList should be operational on your sample page.

## Add a Grid

Now, let's add a Kendo UI Angular Grid.

1. First, [install the Grid npm package and all dependencies]({% slug getstarted_grid %}#toc-installation):

```sh
ng add @progress/kendo-angular-grid
```

2. Import the necessary types and the data service in `src/app/app.component.ts`:

```ts
import { GridDataResult, PageChangeEvent } from "@progress/kendo-angular-grid";
import { SortDescriptor } from "@progress/kendo-data-query";
import { ProductService } from "./product.service";
import { Observable } from "rxjs";
```

3. Add the `ProductService` as a provider in the component declaration:

```ts
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  providers: [ProductService]
})
```

4. Add the `AppComponent` class members that we will use to manipulate the Grid data:

```ts
export class AppComponent {

  // ...

  public gridItems: Observable<GridDataResult>;
  public pageSize: number = 10;
  public skip: number = 0;
  public sortDescriptor: SortDescriptor[] = [];
  public filterTerm: number = null;

  constructor(private service: ProductService) {
    this.loadGridItems();
  }

  public pageChange(event: PageChangeEvent): void {
    this.skip = event.skip;
    this.loadGridItems();
  }

  private loadGridItems(): void {
    this.gridItems = this.service.getProducts(
      this.skip,
      this.pageSize,
      this.sortDescriptor,
      this.filterTerm
    );
  }

  public handleSortChange(descriptor: SortDescriptor[]): void {
    this.sortDescriptor = descriptor;
    this.loadGridItems();
  }

}
```

5. Finally, add the Grid markup in `src/app/app.component.html`, rebuild and check the Grid in your browser:

```html
<kendo-grid [data]="gridItems | async" [pageSize]="pageSize" [skip]="skip" [pageable]="true"
  (pageChange)="pageChange($event)" [sortable]="true" [sort]="sortDescriptor" (sortChange)="handleSortChange($event)"
  [height]="400">
  <kendo-grid-column field="ProductID" title="ID" width="50">
  </kendo-grid-column>
  <kendo-grid-column field="ProductName" title="Product Name">
  </kendo-grid-column>
  <kendo-grid-column field="Category.CategoryName" title="Category">
  </kendo-grid-column>
  <kendo-grid-column field="UnitPrice" title="Unit Price" width="140" format="{0:c}">
  </kendo-grid-column>
  <kendo-grid-column field="Discontinued" width="140" filter="boolean">
    <ng-template kendoGridCellTemplate let-dataItem>
      <input type="checkbox" [checked]="dataItem.Discontinued" disabled />
    </ng-template>
  </kendo-grid-column>
</kendo-grid>
```

The Grid instance in our app can be [sorted]({% slug sorting_grid %}) and [paged]({% slug paging_grid %}). It uses [number formatting]({% slug parsingandformatting_intl %}#toc-number-formatting) for the `UnitPrice` column and checkboxes in a [cell template]({% slug templates_columns_grid %}#toc-cell-template) to display the boolean `Discontinued` field.

## Filter the Grid with the DropDownList

Finally, let's add some component interaction. The Grid has a [built-in filtering UI]({% slug filtering_grid %}), but instead, we will use the DropDownList to filter the Grid by product category. To achieve this, we need to add a [`valueChange` handler]({% slug overview_ddl %}#toc-events):

1. Add the `handleFilterChange` method in `src/app/app.component.ts`:

```ts
export class AppComponent {

  // ...

  public handleFilterChange(item: {
    text: string;
    value: number | null;
  }): void {
    this.filterTerm = item.value;
    this.skip = 0;
    this.loadGridItems();
  }
}
```

2. Bind the `(valueChange)` event of the DropDownList in `src/app/app.component.html`:

```html
<kendo-dropdownlist [data]="dropDownItems" [textField]="'text'" [valueField]="'value'" [defaultItem]="defaultItem" (valueChange)="handleFilterChange($event)">
</kendo-dropdownlist>
```

## App Source Code

Your Kendo UI Angular Getting Started application is complete!

![Kendo UI Angular Getting Started app](./images/getting-started.png)

The complete source code is available in the [kendo-angular-getting-started GitHub repository](https://github.com/telerik/kendo-angular-getting-started).

You can also [run, fork and experiment with the app directly in StackBlitz](https://stackblitz.com/github/telerik/kendo-angular-getting-started).

## Activate Your License Key

Kendo UI for Angular is a professionally developed library distributed under a [commercial license](https://www.telerik.com/purchase/license-agreement/kendo-ui).

Starting from December 2020, [Kendo UI for Angular requires a commercial license key or an active trial license key](https://www.telerik.com/blogs/introducing-license-keys-kendo-ui-for-angular).

Follow the instructions at [Set Up Your License Key]({% slug my_license_angular %}) to activate your license.

**Make sure to exclude the license file from source control.** For example, GitHub users should add `kendo-ui-license.txt` to `.gitignore`.

## Next Steps

You are ready to explore the [Components section](https://www.telerik.com/kendo-angular-ui/components) and discover the amazing features of Kendo UI for Angular!

You may also want to check:

* [Watch the Kendo UI Angular video course]({% slug virtualclass_kendoangular %})
* [Browse more sample applications]({% slug sample_apps %})
* [Get the Kendo UI Angular source code]({% slug source_code_installation %})

Happy coding!
