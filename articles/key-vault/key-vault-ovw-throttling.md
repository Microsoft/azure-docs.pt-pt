---
title: Diretrizes de limitação do Azure Key Vault
description: Limitação do Key Vault limita o número de chamadas simultâneas para impedir o uso excessivo de recursos.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197373"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

Limitação é um processo iniciar que limita o número de chamadas simultâneas para o serviço do Azure para impedir o uso excessivo de recursos. Cofre de chaves do Azure (AKV) foi concebida para lidar com um grande volume de pedidos. Se ocorrer um número massivo de pedidos, limitar os pedidos de seu cliente ajuda a manter a otimizar o desempenho e fiabilidade do serviço AKV.

Limites de limitação variar com base no cenário. Por exemplo, se estiver executando um grande volume de escritas, a possibilidade de limitação é maior do que se estiver a efetuar apenas leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como é que o Cofre de chaves com seus limites?

Os limites de serviço no Key Vault impedem o uso indevido de recursos e garantem a qualidade do serviço para todos os clientes da Key Vault. Quando um limiar de serviço é ultrapassado, o Cofre chave limita quaisquer pedidos adicionais desse cliente por um período de tempo, devolve o código de estado HTTP 429 (pedidos a mais), e o pedido falha. Pedidos falhados que devolvem uma contagem de 429 para os limites de aceleração rastreados pela Key Vault. 

Key Vault foi originalmente projetado para ser usado para armazenar e recuperar os seus segredos na hora de implantação.  O mundo evoluiu, e key vault está sendo usado em tempo de execução para armazenar e recuperar segredos, e muitas vezes apps e serviços querem usar Key Vault como uma base de dados.  Os limites atuais não suportam taxas de entrada elevadas.

Key Vault foi originalmente criado com os limites especificados nos limites de serviço do [Cofre chave Azure](key-vault-service-limits.md).  Para maximizar o seu Cofre chave através de taxas de colocação, aqui estão algumas diretrizes/boas práticas recomendadas para maximizar a sua entrada:
1. Certifique-se de que tem estrangulamento no lugar.  O cliente deve honrar as políticas de back-off exponenciais para 429's e garantir que está a fazer repetições de acordo com a orientação abaixo.
1. Divida o tráfego do cofre entre vários cofres e diferentes regiões.   Utilize um cofre separado para cada domínio de segurança/disponibilidade.   Se você tem cinco apps, cada uma em duas regiões, então recomendamos 10 cofres cada um contendo os segredos exclusivos para app e região.  Um limite de subscrição para todos os tipos de transações é cinco vezes o limite individual do cofre. Por exemplo, outras transações HSM por subscrição estão limitadas a 5.000 transações em 10 segundos por subscrição. Considere o cache do segredo dentro do seu serviço ou app para também reduzir o RPS diretamente para o cofre chave e/ou manusear o tráfego baseado em explosão.  Também pode dividir o seu tráfego entre diferentes regiões para minimizar a latência e usar uma subscrição/cofre diferente.  Não envie mais do que o limite de subscrição do serviço Key Vault numa única região do Azure.
1. Cache os segredos que você recupera do Cofre chave Azure na memória, e reutilizar da memória sempre que possível.  Releia a partir do Cofre de Chaves Azure apenas quando a cópia em cache deixar de funcionar (por exemplo, porque foi rodada na fonte). 
1. Key Vault foi projetado para os seus próprios segredos de serviços.   Se estiver a armazenar os segredos dos seus clientes (especialmente para cenários de armazenamento chave de alta suposição), considere colocar as chaves numa base de dados ou numa conta de armazenamento com encriptação e armazenar apenas a chave principal no Cofre chave azure.
1. Criptografe, envolva e verifique que as operações de chave pública podem ser realizadas sem acesso ao Key Vault, o que não só reduz o risco de estrangulamento, como também melhora a fiabilidade (desde que guarde adequadamente o material chave do público).
1. Se utilizar o Key Vault para armazenar credenciais para um serviço, verifique se esse serviço suporta a Autenticação AAD para autenticar diretamente. Isto reduz a carga no Cofre chave, melhora a fiabilidade e simplifica o seu código, uma vez que o Key Vault pode agora usar o token AAD.  Muitos serviços passaram a usar a AAD Auth.  Consulte a lista atual nos [Serviços que suportam identidades geridas para os recursos do Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Considere escalonar a sua carga/implantação durante um período mais longo de tempo para se manter abaixo dos limites atuais de RPS.
1. Se a sua aplicação compreende vários nós que precisam de ler os mesmos segredos, então considere usar um padrão de saída de ventilador, onde uma entidade lê o segredo de Key Vault, e os fãs para todos os nós.   Cache os segredos recuperados apenas na memória.
Se descobrir que o acima ainda não satisfaz as suas necessidades, preencha a tabela abaixo e contacte-nos para determinar que capacidade adicional pode ser adicionada (exemplo abaixo colocado abaixo apenas para fins ilustrativos).

| Nome do cofre | Região do Cofre | Tipo de objeto (Segredo, Chave ou Cert) | Operação(s)* | Tipo chave | Comprimento da chave ou curva | Chave HSM?| RPS de estado estável necessário | RpS de pico necessário |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chave | Assinar | EC | P-256 | Não | 200 | 1000 |

\* Para obter uma lista completa de valores possíveis, consulte [as operações do Cofre chave do Azure.](/rest/api/keyvault/key-operations)

Se for aprovada uma capacidade adicional, note o seguinte como resultado do aumento da capacidade:
1. O modelo de consistência de dados muda. Uma vez que um cofre é permitido listado com capacidade de entrada adicional, a garantia de consistência de dados do serviço Key Vault altera as alterações (necessárias para atender rpS de volume mais elevado, uma vez que o serviço de armazenamento azure subjacente não consegue acompanhar).  Em poucas palavras:
  1. **Sem permitir a listagem**: O serviço Key Vault refletirá os resultados de uma operação de escrita (por exemplo. SecretSet, CreateKey) imediatamente em chamadas subsequentes (por exemplo. SecretGet, KeySign).
  1. **Com a listagem de permitines**: O serviço Key Vault refletirá os resultados de uma operação de escrita (por exemplo. SecretSet, CreateKey) dentro de 60 segundos em chamadas subsequentes (por exemplo. SecretGet, KeySign).
1. O código do cliente deve honrar a política de back-off por 429 repetições. O código do cliente que liga para o serviço Key Vault não deve rejulgar imediatamente os pedidos do Key Vault quando receber um código de resposta 429.  A orientação de estrangulamento do Cofre-Chave Azure publicada aqui recomenda a aplicação de backoff exponencial ao receber um código de resposta http 429.

Se tiver um caso comercial válido para limites de limitação mais elevados, entre em contato conosco.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como limitar a sua aplicação em resposta a limites de serviço

Seguem-se **as melhores práticas** que deve implementar quando o seu serviço está estrangulado:
- Reduza o número de operações por pedido.
- Reduza a frequência de pedidos.
- Evite as repetições imediatas. 
    - Todos os pedidos de acumulam em relação a seus limites de utilização.

Quando implementar o tratamento de erros da sua aplicação, utilize o código de erro HTTP 429 para detetar a necessidade de limitação do lado do cliente. Se o pedido falhar novamente com um código de erro HTTP 429, tiver ainda com um limite de serviço do Azure. Continue a utilizar o recomendado lado do cliente a limitação de método, repetir o pedido até ter êxito.

Código que implementa um término exponencial é mostrado abaixo. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


A utilização deste C# código numa aplicação de cliente é simples. 

### <a name="recommended-client-side-throttling-method"></a>Método de limitação do lado do cliente recomendado

No código de erro HTTP 429, começar o seu cliente usando uma abordagem de término exponencial de limitação:

1. Aguarde 1 segundo, o pedido de repetição
2. Se ainda otimizado espera 2 segundos, repita o pedido
3. Se ainda otimizado espera 4 segundos, repita o pedido
4. Se ainda otimizado espera 8 segundos, repita o pedido
5. Se ainda otimizado espera 16 segundos, repita o pedido

Neste ponto, deverá não receber códigos de resposta de HTTP 429.

## <a name="see-also"></a>Consulte também

Para uma orientação mais profunda de estrangulamento na Nuvem microsoft, consulte [o Padrão de Estrangulamento](https://docs.microsoft.com/azure/architecture/patterns/throttling).

