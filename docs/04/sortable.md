# Sortable Solution

## Model

The model should have a filed to save the `order

```python
order = models.PositiveIntegerField(default=1000, editable=False, db_index=True)
```

## Sortable.js

We can build a Stimulus controller as a wrapper of the [Sortable.js](https://github.com/SortableJS/Sortable)

An example can be found on [stimulus-sortable](https://github.com/stimulus-components/stimulus-sortable)

## Save Order

To save order on the server side, we can send Ajax request to the server when the order is changed.

```python
def sortable_list_order_view(request):
    """
    Update order of the sortable list
    """
    data = json.loads(request.body)
    for index, item in enumerate(data):
        DemoTask.objects.filter(pk=item["id"]).update(order=index)

    return HttpResponse("OK")
```

## Demo

[https://saashammer.com/demo-tasks/sortable-list/](https://saashammer.com/demo-tasks/sortable-list/)

## References

[How to let user re-order/sort table of content with drag and drop](https://nemecek.be/blog/4/django-how-to-let-user-re-ordersort-table-of-content-with-drag-and-drop)
