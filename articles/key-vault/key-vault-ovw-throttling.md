---
title: Diretrizes de limitação do Azure Key Vault
description: Limitação do Key Vault limita o número de chamadas simultâneas para impedir o uso excessivo de recursos.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806682"
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

Limitação é um processo iniciar que limita o número de chamadas simultâneas para o serviço do Azure para impedir o uso excessivo de recursos. Cofre de chaves do Azure (AKV) foi concebida para lidar com um grande volume de pedidos. Se ocorrer um número massivo de pedidos, limitar os pedidos de seu cliente ajuda a manter a otimizar o desempenho e fiabilidade do serviço AKV.

Limites de limitação variar com base no cenário. Por exemplo, se estiver executando um grande volume de escritas, a possibilidade de limitação é maior do que se estiver a efetuar apenas leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como é que o Cofre de chaves com seus limites?

Os limites de serviço no Key Vault impedem o uso indevido de recursos e garantem a qualidade do serviço para todos os clientes do Key Vault. Quando um limite de serviço é excedido, Key Vault limita qualquer solicitação adicional desse cliente por um período de tempo, retorna o código de status HTTP 429 (muitas solicitações) e a solicitação falha. Solicitações com falha que retornam uma contagem de 429 em direção aos limites de limitação rastreados por Key Vault. 

O Key Vault foi originalmente projetado para ser usado para armazenar e recuperar seus segredos no momento da implantação.  O mundo evoluiu e Key Vault está sendo usado em tempo de execução para armazenar e recuperar segredos e, muitas vezes, aplicativos e serviços desejam usar Key Vault como um banco de dados.  Os limites atuais não dão suporte a altas taxas de taxa de transferência.

O Key Vault foi criado originalmente com os limites especificados nos [limites de serviço Azure Key Vault](key-vault-service-limits.md).  Para maximizar sua Key Vault por meio de tarifas de put, aqui estão algumas diretrizes recomendadas/práticas recomendadas para maximizar sua taxa de transferência:
1. Verifique se você tem limitação em vigor.  O cliente deve honrar as políticas de retirada exponencial Para 429 e garantir que você esteja fazendo novas tentativas de acordo com as diretrizes abaixo.
1. Divida seu tráfego de Key Vault entre vários cofres e regiões diferentes.   Use um cofre separado para cada domínio de segurança/disponibilidade.   Se você tiver cinco aplicativos, cada um em duas regiões, recomendamos 10 cofres que contenham os segredos exclusivos do aplicativo e da região.  Um limite de toda a assinatura para todos os tipos de transação é cinco vezes o limite individual do cofre de chaves. Por exemplo, HSM-outras transações por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura. Considere armazenar em cache o segredo em seu serviço ou aplicativo para também reduzir o RPS diretamente para o cofre de chaves e/ou lidar com o tráfego baseado em intermitência.  Você também pode dividir o tráfego entre regiões diferentes para minimizar a latência e usar uma assinatura/cofre diferente.  Não envie mais do que o limite de assinatura para o serviço de Key Vault em uma única região do Azure.
1. Armazene em cache os segredos que você recupera de Azure Key Vault na memória e reutilize a memória sempre que possível.  Leia novamente de Azure Key Vault somente quando a cópia armazenada em cache parar de funcionar (por exemplo, porque ela foi girada na origem). 
1. Key Vault é projetado para seus próprios segredos de serviços.   Se você estiver armazenando os segredos de seus clientes (especialmente para cenários de armazenamento de chaves de alta taxa de transferência), considere colocar as chaves em um banco de dados ou conta de armazenamento com criptografia e armazenar apenas a chave mestra em Azure Key Vault.
1. As operações criptografar, encapsular e verificar a chave pública podem ser executadas sem acesso ao Key Vault, o que não apenas reduz o risco de limitação, mas também melhora a confiabilidade (desde que você armazene o material da chave pública em cache corretamente).
1. Se você usar Key Vault para armazenar credenciais para um serviço, verifique se esse serviço oferece suporte à autenticação do AAD para autenticar diretamente. Isso reduz a carga em Key Vault, melhora a confiabilidade e simplifica seu código, pois Key Vault agora pode usar o token do AAD.  Muitos serviços foram movidos para usando a autenticação do AAD.  Consulte a lista atual em [serviços que dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Considere a possibilidade de escalonar sua carga/implantação em um período de tempo maior para permanecer sob os limites atuais do RPS.
1. Se seu aplicativo for composto por vários nós que precisam ler os mesmos segredos, considere usar um padrão de Fan out, em que uma entidade lê o segredo de Key Vault e os fãs para todos os nós.   Armazene os segredos recuperados somente na memória.
Se você descobrir que o acima ainda não atende às suas necessidades, preencha a tabela abaixo e entre em contato conosco para determinar a capacidade adicional que pode ser adicionada (exemplo, coloque-se abaixo apenas para fins ilustrativos).

| Nome do cofre | Região do cofre | Tipo de objeto (segredo, chave ou certificado) | Operação (ões) * | Tipo de chave | Comprimento ou curva de chave | Chave HSM?| RPS de estado estável necessário | Máximo de RPS necessário |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chave | Assinar | EC | P-256 | Não | 200 | 1000 |

\* para obter uma lista completa de valores possíveis, consulte [Azure Key Vault operações](/rest/api/keyvault/key-operations).

Se a capacidade adicional for aprovada, observe o seguinte como resultado da capacidade aumentar:
1. Alterações no modelo de consistência de dados. Depois que um cofre é permitido listado com capacidade de taxa de transferência adicional, a Key Vault garantia de consistência de dados de serviço é alterada (necessário para atender a um maior volume RPS, já que o serviço de armazenamento do Azure subjacente não pode acompanhar).  Em resumo:
  1. **Sem a listagem de permissão**: o serviço de Key Vault refletirá os resultados de uma operação de gravação (por exemplo, Secretset, CreateKey) imediatamente nas chamadas subsequentes (por exemplo, SecretGet, keysign).
  1. **Com a listagem de permissão**: o serviço de Key Vault refletirá os resultados de uma operação de gravação (por exemplo, Secretset, CreateKey) dentro de 60 segundos em chamadas subsequentes (por exemplo, SecretGet, keysign).
1. O código do cliente deve honrar a política de retirada Para 429 tentativas. O código do cliente que chama o serviço de Key Vault não deve repetir Key Vault solicitações imediatamente quando recebe um código de resposta 429.  As diretrizes de limitação de Azure Key Vault publicadas aqui recomendam a aplicação de retirada exponencial ao receber um código de resposta http 429.

Se tiver um caso comercial válido para limites de limitação mais elevados, entre em contato conosco.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como limitar a sua aplicação em resposta a limites de serviço

Veja a seguir as **práticas recomendadas** que você deve implementar quando o serviço for limitado:
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


O uso desse código em um C# aplicativo cliente é simples. 

### <a name="recommended-client-side-throttling-method"></a>Método de limitação do lado do cliente recomendado

No código de erro HTTP 429, começar o seu cliente usando uma abordagem de término exponencial de limitação:

1. Aguarde 1 segundo, o pedido de repetição
2. Se ainda otimizado espera 2 segundos, repita o pedido
3. Se ainda otimizado espera 4 segundos, repita o pedido
4. Se ainda otimizado espera 8 segundos, repita o pedido
5. Se ainda otimizado espera 16 segundos, repita o pedido

Neste ponto, deverá não receber códigos de resposta de HTTP 429.

## <a name="see-also"></a>Ver também

Para uma orientação mais aprofundada de limitação na Cloud da Microsoft, consulte [padrão de limitação](https://docs.microsoft.com/azure/architecture/patterns/throttling).

