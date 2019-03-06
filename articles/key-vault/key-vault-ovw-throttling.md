---
title: Diretrizes de limitação do Azure Key Vault
description: Limitação do Key Vault limita o número de chamadas simultâneas para impedir o uso excessivo de recursos.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 823eebeddb64c15ef20d103f2f9290c800753f1a
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404766"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

Limitação é um processo iniciar que limita o número de chamadas simultâneas para o serviço do Azure para impedir o uso excessivo de recursos. Cofre de chaves do Azure (AKV) foi concebida para lidar com um grande volume de pedidos. Se ocorrer um número massivo de pedidos, limitar os pedidos de seu cliente ajuda a manter a otimizar o desempenho e fiabilidade do serviço AKV.

Limites de limitação variar com base no cenário. Por exemplo, se estiver executando um grande volume de escritas, a possibilidade de limitação é maior do que se estiver a efetuar apenas leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como é que o Cofre de chaves com seus limites?

Limites de serviço do Cofre de chaves estão lá impedir a utilização indevida dos recursos e garantir a qualidade de serviço para todos os clientes de Key Vault. Quando um limiar de serviço for excedido, o Key Vault limita os pedidos adicionais desse cliente durante um período de tempo. Quando isto acontecer, o Key Vault retorna o código de estado HTTP 429 (demasiados pedidos), e os pedidos falharem. Além disso, pedidos falhados que devolvem uma contagem 429 para os limites de limitação controlados pelo Cofre de chaves. 

Se tiver um caso comercial válido para limites de limitação mais elevados, entre em contato conosco.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como limitar a sua aplicação em resposta a limites de serviço

Seguem-se **melhores práticas** para a sua aplicação de limitação de:
- Reduza o número de operações por pedido.
- Reduza a frequência de pedidos.
- Evite as repetições imediatas. 
    - Todos os pedidos de acumulam em relação a seus limites de utilização.

Quando implementar o tratamento de erros da sua aplicação, utilize o código de erro HTTP 429 para detetar a necessidade de limitação do lado do cliente. Se o pedido falhar novamente com um código de erro HTTP 429, tiver ainda com um limite de serviço do Azure. Continue a utilizar o recomendado lado do cliente a limitação de método, repetir o pedido até ter êxito.

Código que implementa um término exponencial é mostrado abaixo. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Usando este código num cliente C\# aplicativo (outro microsserviços de cliente de Web API, um aplicativo ASP.NET MVC ou até mesmo um C\# aplicação Xamarin) é muito simples. A exemplo a seguir mostra como, usando a classe HttpClient.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Lembre-se de que esse código é adequado apenas como uma prova de conceito. 

### <a name="recommended-client-side-throttling-method"></a>Método de limitação do lado do cliente recomendado

No código de erro HTTP 429, começar o seu cliente usando uma abordagem de término exponencial de limitação:

1. Aguarde 1 segundo, o pedido de repetição
2. Se ainda otimizado espera 2 segundos, repita o pedido
3. Se ainda otimizado espera 4 segundos, repita o pedido
4. Se ainda otimizado espera 8 segundos, repita o pedido
5. Se ainda otimizado espera 16 segundos, repita o pedido

Neste ponto, deverá não receber códigos de resposta de HTTP 429.

## <a name="see-also"></a>Consulte também

Para uma orientação mais aprofundada de limitação na Cloud da Microsoft, consulte [padrão de limitação](https://docs.microsoft.com/azure/architecture/patterns/throttling).

