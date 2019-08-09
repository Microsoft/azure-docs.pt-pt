---
title: Replicando geograficamente um registro de contêiner do Azure
description: Introdução à criação e ao gerenciamento de registros de contêiner do Azure com replicação geográfica.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: 2fffa3b063969cbe68fb9a405f4198f15b3f9809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845203"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georreplicação no Azure Container Registry

As empresas que desejam uma presença local ou um backup dinâmico, optam por executar serviços de várias regiões do Azure. Como prática recomendada, colocar um registro de contêiner em cada região em que as imagens são executadas permite operações de fechamento de rede, permitindo transferências de camada de imagem rápidas e confiáveis. A replicação geográfica permite que um registro de contêiner do Azure funcione como um registro único, servindo várias regiões com registros regionais de vários mestres. 

Um registro com replicação geográfica oferece os seguintes benefícios:

* Nomes únicos de registro/imagem/marca podem ser usados em várias regiões
* Acesso ao registro de fechamento de rede de implantações regionais
* Não há taxas de egresso adicionais, pois as imagens são extraídas de um registro local replicado na mesma região que o host do contêiner
* Gerenciamento único de um registro em várias regiões

> [!NOTE]
> Se você precisar manter cópias de imagens de contêiner em mais de um registro de contêiner do Azure, o registro de contêiner do Azure também dará suporte à [importação de imagem](container-registry-import-images.md). Por exemplo, em um fluxo de trabalho do DevOps, você pode importar uma imagem de um registro de desenvolvimento para um registro de produção, sem a necessidade de usar comandos do Docker.
>

## <a name="example-use-case"></a>Caso de uso de exemplo
A contoso executa um site de presença pública localizado nos EUA, no Canadá e na Europa. Para atender a esses mercados com conteúdo local e de fechamento de rede, a contoso executa clusters do AKS ( [serviço kubernetes do Azure](/azure/aks/) ) no oeste dos EUA, leste dos EUA, Canadá Central e Europa Ocidental. O aplicativo de site, implantado como uma imagem do Docker, utiliza o mesmo código e imagem em todas as regiões. O conteúdo, local para essa região, é recuperado de um banco de dados, que é provisionado exclusivamente em cada região. Cada implantação regional tem sua configuração exclusiva para recursos como o banco de dados local.

A equipe de desenvolvimento está localizada em Seattle WA, utilizando o data center oeste dos EUA.

![Enviando por push para vários registros](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Enviando por push para vários registros*

Antes de usar os recursos de replicação geográfica, a Contoso tinha um registro baseado nos EUA no oeste dos EUA, com um registro adicional no Europa Ocidental. Para atender a essas regiões diferentes, a equipe de desenvolvimento enviou imagens para dois registros diferentes.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Pull de vários registros](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Pull de vários registros*

Os desafios típicos de vários registros incluem:

* Todos os clusters dos EUA, oeste dos EUA e Canadá central são todos extraídos do registro oeste dos EUA, incorrendo em tarifas de saída, pois cada um desses contêineres remotos hospedam imagens de pull de data centers oeste dos EUA.
* A equipe de desenvolvimento deve enviar imagens por push para os registros oeste dos EUA e Europa Ocidental.
* A equipe de desenvolvimento deve configurar e manter cada implantação regional com nomes de imagem que fazem referência ao registro local.
* O acesso ao registro deve ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Benefícios da replicação geográfica

![Extraindo de um registro replicado geograficamente](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Usando o recurso de replicação geográfica do registro de contêiner do Azure, esses benefícios são percebidos:

* Gerenciar um único registro em todas as regiões:`contoso.azurecr.io`
* Gerencie uma única configuração de implantações de imagem, pois todas as regiões usaram a mesma URL de imagem:`contoso.azurecr.io/public/products/web:1.2`
* Enviar por push para um único registro, enquanto o ACR gerencia a replicação geográfica. Você pode configurar WebHooks regionais para notificá-lo de eventos em réplicas específicas. [](container-registry-webhook.md)

## <a name="configure-geo-replication"></a>Configurar georreplicação

Configurar a replicação geográfica é tão fácil quanto clicar em regiões em um mapa. Você também pode gerenciar a replicação geográfica usando ferramentas que incluem os comandos [AZ ACR Replication](/cli/azure/acr/replication) na CLI do Azure.

A replicação geográfica é um recurso somente de [registros Premium](container-registry-skus.md) . Se o registro ainda não for Premium, você poderá alterar de básico e padrão para Premium no [portal do Azure](https://portal.azure.com):

![Alternando SKUs no portal do Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a replicação geográfica para o registro Premium, faça logon no portal do Azure em https://portal.azure.com.

Navegue até o registro de contêiner do Azure eselecione replicações:

![Replicações na IU de registo de contentor do portal do Azure](media/container-registry-geo-replication/registry-services.png)

Um mapa é exibido mostrando todas as regiões atuais do Azure:

 ![Mapa de região no portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexágonos azuis representam réplicas atuais
* Hexágonos verdes representam possíveis regiões de réplica
* Hexágonos cinzas representam regiões do Azure ainda não disponíveis para replicação

Para configurar uma réplica, selecione uma hexágono verde e, em seguida, selecione **criar**:

 ![Criar a IU de replicação no portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionais, selecione os hexágonos verdes para outras regiões e clique em **criar**.

O ACR começa a sincronizar imagens entre as réplicas configuradas. Após a conclusão, o portal refletirá *pronto*. O status da réplica no portal não é atualizado automaticamente. Use o botão atualizar para ver o status atualizado.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerações sobre o uso de um registro replicado geograficamente

* Cada região em um registro com replicação geográfica é independente uma vez configurada. Os SLAs do registro de contêiner do Azure se aplicam a cada região replicada geograficamente.
* Quando você envia por Push ou efetua pull de imagens de um registro com replicação geográfica, o Gerenciador de tráfego do Azure em segundo plano envia a solicitação para o registro localizado na região mais próxima de você.
* Depois de enviar uma imagem ou uma atualização de marca para a região mais próxima, leva algum tempo para o registro de contêiner do Azure replicar os manifestos e as camadas nas regiões restantes que você aceitou. Imagens maiores levam mais tempo para serem replicadas do que as menores. Imagens e marcas são sincronizadas nas regiões de replicação com um modelo de consistência eventual.
* Para gerenciar fluxos de trabalho que dependem de atualizações de push para um registro replicado geograficamente, recomendamos que [](container-registry-webhook.md) você configure WebHooks para responder aos eventos de push. Você pode configurar WebHooks regionais em um registro replicado geograficamente para rastrear eventos de push à medida que eles são concluídos em regiões com replicação geográfica.


## <a name="geo-replication-pricing"></a>Preços de replicação geográfica

A replicação geográfica é um recurso da [SKU Premium](container-registry-skus.md) do registro de contêiner do Azure. Ao replicar um registro para as regiões desejadas, você incorre em taxas de registro Premium para cada região.

No exemplo anterior, a contoso consolidou dois registros em um, adicionando réplicas ao leste dos EUA, ao Canadá central e à Europa Ocidental. A contoso pagará quatro vezes a Premium por mês, sem nenhuma configuração ou gerenciamento adicional. Cada região agora efetua pull de suas imagens localmente, melhorando o desempenho, a confiabilidade, sem tarifas de saída de rede do oeste dos EUA para o Canadá e o leste dos EUA.

## <a name="next-steps"></a>Passos Seguintes

Confira a série de tutoriais de três partes, [replicação geográfica no registro de contêiner do Azure](container-registry-tutorial-prepare-registry.md). Percorra a criação de um registro replicado geograficamente, a criação de um contêiner e a implantação de um `docker push` único comando para vários aplicativos Web regionais para instâncias de contêineres.

> [!div class="nextstepaction"]
> [Replicação geográfica no registro de contêiner do Azure](container-registry-tutorial-prepare-registry.md)
