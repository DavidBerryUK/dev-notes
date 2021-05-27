# Api Controllers

Controllers are the entry point into an API application.

## Rules
* Should only accept and return ApiModels, never database entity models.
* Should not do any processing, call a service and return results.
* Should be decorated with a verb, such as Get or Post.
* Should use dependancy injection to use services.
* If performs function such as database access, use async and Tasks to pervent resource locking.


```c#
[ApiController]
[Route("orders")]
public class OrdersController : Controller
{
    private readonly IOrderSaveService _orderSaveService;

    public OrdersController(IOrderSaveService orderSaveService)
    {
        _orderSaveService = orderSaveService;
    }

    [HttpPost]
    public async Task< ActionResult<OrderApiModel>> NewOrder([FromBody] OrderApiModel order)
    {
        var response = await _orderSaveService.SaveAsync(order);
        return Ok(response);
    }

    [HttpGet("open")]
    public async Task<ActionResult<OrderApiModel>> GetOpenOrders()
    {
        var response = await _orderSaveService.GetOpenOrders();
        return Ok(response);
    }
}
```