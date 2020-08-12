# 苹果内购

## 安装

composer

```bash
$ php composer.phar require minz/apple-pay
```


## 使用
```php
// $receipt apple支付凭据
$receipt = request()->get('receipt');
// 内购商品id
$productId = request()->get('productId');
// notice 如果商品为续订类产品,password需要输入 提供的共享密码,否则无需传递参数
$applePay = new ApplePay($receipt, $password);
if ($applePay->verifyReceipt(true)) {
    $result = $applePay->query($productId, function ($tradeNo, $returnData) use ($productId) {
        // 检查此交易号是否被使用
        $transaction = Transaction::find($tradeNo);
        if ($transaction) {
            throw new Exception("此笔交易号已经被使用");
        }
    
        //进行本身业务操作
        DB::transaction(function () use ($tradeNo, $productId) {
            //.....
            //db操作
        });
        return true;
    });
    if (!$result) {
        throw new Exception("app上报productId与apple返回数据不统一");
    }
    // 验证成功...
    // return response
} else {
    throw new Exception("applePay验证异常,请关注"); 
}
```