---
title: Diretrizes de limitação do Azure Key Vault
description: O estrangulamento do Cofre-Chave limita o número de chamadas simultâneas para evitar o uso excessivo de recursos.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749540"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

O Throttling é um processo que inicia que limita o número de chamadas simultâneas ao serviço Azure para evitar o uso excessivo de recursos. O Azure Key Vault (AKV) foi concebido para suportar um grande volume de pedidos. Se ocorrer um número esmagador de pedidos, o estrangulamento dos pedidos do seu cliente ajuda a manter o melhor desempenho e fiabilidade do serviço AKV.

Os limites de estrangulamento variam em função do cenário. Por exemplo, se estiver a executar um grande volume de escritas, a possibilidade de estrangulamento é maior do que se estiver apenas a executar leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como é que o Key Vault lida com os seus limites?

Os limites de serviço no Key Vault impedem o uso indevido de recursos e garantem a qualidade do serviço a todos os clientes da Key Vault. Quando um limiar de serviço é ultrapassado, o Key Vault limita quaisquer outros pedidos desse cliente por um período de tempo, devolve o código de estado HTTP 429 (Demasiados pedidos) e o pedido falha. Os pedidos falhados que devolvem um 429 não contam para os limites de aceleração seguidos pelo Cofre de Chaves. 

Key Vault foi originalmente projetado para ser usado para armazenar e recuperar os seus segredos na hora de implantação.  O mundo evoluiu, e o Key Vault está a ser usado em tempo de execução para armazenar e recuperar segredos, e muitas vezes aplicações e serviços querem usar o Key Vault como uma base de dados.  Os limites atuais não suportam taxas de produção elevadas.

O Key Vault foi originalmente criado com os limites especificados nos limites de [serviço do Azure Key Vault](service-limits.md).  Para maximizar o seu Cofre-Chave através de taxas de put, aqui estão algumas diretrizes/melhores práticas recomendadas para maximizar o seu rendimento:
1. Certifique-se de que tem estrangulamento no lugar.  O cliente deve honrar as políticas de back-off exponencial para 429's e garantir que está a fazer recauchutagem de acordo com as orientações abaixo.
1. Divida o seu tráfego key vault entre vários cofres e diferentes regiões.   Utilize um cofre separado para cada domínio de segurança/disponibilidade.   Se você tem cinco aplicativos, cada um em duas regiões, então recomendamos 10 cofres cada um contendo os segredos exclusivos para app e região.  Um limite de subscrição para todos os tipos de transação é cinco vezes superior ao limite de cofre de chaves individual. Por exemplo, as transações HSM-other por subscrição estão limitadas a 5.000 transações em 10 segundos por subscrição. Considere caching o segredo dentro do seu serviço ou app para também reduzir o RPS diretamente para o cofre chave e/ou manusear o tráfego baseado em explosão.  Também pode dividir o seu tráfego entre diferentes regiões para minimizar a latência e usar uma subscrição/cofre diferente.  Não envie mais do que o limite de subscrição para o serviço Key Vault numa única região de Azure.
1. Cache os segredos que você recupera do Cofre da Chave Azure na memória, e reutilizar da memória sempre que possível.  Relê-lo do Azure Key Vault apenas quando a cópia em cache deixar de funcionar (por exemplo, porque foi rodada na fonte). 
1. O Key Vault foi concebido para os seus próprios segredos de serviços.   Se estiver a armazenar os segredos dos seus clientes (especialmente para cenários de armazenamento de chaves de alto rendimento), considere colocar as chaves numa base de dados ou uma conta de armazenamento com encriptação e armazenar apenas a chave principal no Cofre da Chave Azure.
1. Criptografe, envolva e verifique se as operações de chave pública podem ser realizadas sem acesso ao Key Vault, o que não só reduz o risco de estrangulamento, como também melhora a fiabilidade (desde que cache corretamente o material chave público).
1. Se utilizar o Key Vault para armazenar credenciais para um serviço, verifique se esse serviço suporta a autenticação AZure AD para autenticar diretamente. Isto reduz a carga no Key Vault, melhora a fiabilidade e simplifica o seu código, uma vez que o Key Vault pode agora utilizar o token AD Azure.  Muitos serviços passaram a usar a Azure AD Auth.  Consulte a lista atual dos [Serviços que suportam identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Considere espantosamente a sua carga/implantação durante um período de tempo mais longo para se manter abaixo dos limites atuais do RPS.
1. Se a sua aplicação compreende vários nós que precisam de ler os mesmos segredos, então considere usar um padrão de saída de ventilador, onde uma entidade lê o segredo de Key Vault, e os fãs para todos os nós.   Cache os segredos recuperados apenas na memória.
Se descobrir que o acima ainda não satisfaz as suas necessidades, preencha a tabela abaixo e contacte-nos para determinar que capacidade adicional pode ser adicionada (por exemplo, colocado abaixo apenas para fins ilustrativos).

| Nome do Vault | Região do Cofre | Tipo de objeto (Segredo, Chave ou Cert) | Operação(s)* | Tipo chave | Comprimento ou curva da chave | Chave HSM?| RPS de estado estável necessário | Pico RPS necessário |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chave | Assinar | EC | P-256 | No | 200 | 1000 |

\* Para obter uma lista completa de valores possíveis, consulte as [operações do Azure Key Vault](/rest/api/keyvault/key-operations).

Se for aprovada a capacidade adicional, por favor, note o seguinte como resultado do aumento da capacidade:
1. Alterações no modelo de consistência de dados. Uma vez que um cofre é permitido listado com capacidade adicional de produção, o serviço Key Vault de consistência de dados altera alterações (necessária para atender RPS de volume mais elevado uma vez que o serviço de armazenamento Azure subjacente não consegue acompanhar).  Em poucas palavras:
  1. **Sem permitir a listagem**: O serviço Key Vault refletirá os resultados de uma operação de escrita (por exemplo. SecretSet, CreateKey) imediatamente em chamadas subsequentes (por exemplo. SecretGet, KeySign).
  1. **Com a listagem de permitis**: O serviço Key Vault refletirá os resultados de uma operação de escrita (por exemplo. SecretSet, CreateKey) dentro de 60 segundos em chamadas subsequentes (por exemplo. SecretGet, KeySign).
1. O código do cliente deve honrar a política de back-off para 429 retróscos. O código do cliente que chama o serviço Key Vault não deve re-tentar imediatamente os pedidos do Key Vault quando recebe um código de resposta 429.  A orientação de estrangulamento do Azure Key Vault publicada aqui recomenda a aplicação de backoff exponencial ao receber um código de resposta 429 Http.

Se tiver um caso de negócio válido para limites de aceleração mais elevados, entre em contato conosco.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como acelerar a sua app em resposta aos limites de serviço

Seguem-se **as melhores práticas** que deve implementar quando o seu serviço é acelerado:
- Reduza o número de operações por pedido.
- Reduza a frequência dos pedidos.
- Evite recauchutagens imediatas. 
    - Todos os pedidos se acumulam contra os seus limites de utilização.

Quando implementar o tratamento de erros da sua aplicação, utilize o código de erro HTTP 429 para detetar a necessidade de estrangulamento do lado do cliente. Se o pedido falhar novamente com um código de erro HTTP 429, ainda encontra um limite de serviço Azure. Continue a utilizar o método de estrangulamento recomendado do lado do cliente, reformulando o pedido até que tenha sucesso.

O código que implementa o backoff exponencial é mostrado abaixo. 
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
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


A utilização deste código numa aplicação C# do cliente é simples. 

### <a name="recommended-client-side-throttling-method"></a>Método de estrangulamento recomendado do lado do cliente

No código de erro HTTP 429, comece a estrangular o seu cliente utilizando uma abordagem exponencial de backoff:

1. Espere 1 segundo, redando o pedido
2. Se ainda estrangulado esperar 2 segundos, reda o pedido de novo
3. Se ainda estrangulado esperar 4 segundos, reda o pedido de novo
4. Se ainda estrangulado esperar 8 segundos, reda o pedido de novo
5. Se ainda estrangulado esperar 16 segundos, reda o pedido de novo

Neste momento, não deverá receber códigos de resposta HTTP 429.

## <a name="see-also"></a>Ver também

Para uma orientação mais profunda de estrangulamento na Cloud microsoft, consulte o [Padrão de Aceleração](/azure/architecture/patterns/throttling).
