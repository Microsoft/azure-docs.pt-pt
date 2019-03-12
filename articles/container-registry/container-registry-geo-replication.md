---
title: Replicação geográfica de um Azure container registry
description: Introdução à criação e gestão de registos de contentor do Azure georreplicado.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: 2dc314dd1d1e728f03c1d0c660d9339254ddc462
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541864"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georreplicação no Azure Container Registry

As empresas que pretendem uma presença local ou uma cópia de segurança de acesso frequente, optar por executar serviços a partir de várias regiões do Azure. Como melhor prática, a colocação de um registo de contentor em cada região onde as imagens são executadas permite operações de perto da rede, rápida e ativar as transferências de camada de imagem fiável. A georreplicação permite que um Azure container registry a funcionar como um registo único, que serve várias regiões com vários mestres registos regionais.

Um registo georreplicado fornece as seguintes vantagens:

* Nomes de registo/imagem/etiqueta únicos podem ser utilizados em várias regiões
* Acesso de registo de perto da rede de implementações regionais
* Sem taxas de saída adicionais, como imagens são extraídas de um registo de local, replicado na mesma região que o anfitrião do contentor
* Gestão única de um registo por várias regiões

> [!NOTE]
> Se precisar de manter cópias das imagens de contentor em mais do que um registo de contentor do Azure, Azure Container Registry também suporta [importação de imagens](container-registry-import-images.md). Por exemplo, um fluxo de trabalho do DevOps, pode importar uma imagem de um registo de desenvolvimento para um registo de produção, sem a necessidade de utilizar comandos do Docker.
>

## <a name="example-use-case"></a>Caso de utilização de exemplo
Contoso é executado um site de presença pública localizado nos EUA, Canadá e na Europa. Para atender a esses mercados com conteúdo local e perto da rede, a Contoso é executado [do Azure Kubernetes Service](/azure/aks/) (AKS) clusters no Oeste dos E.U.A., este dos E.U.A., Canadá Central e Europa Ocidental. A aplicação do Web site, implementada como uma imagem do Docker, utiliza o mesmo código e a mesma imagem em todas as regiões. Conteúdo, local para essa região, é obtido a partir de uma base de dados, o que está aprovisionado de forma exclusiva em cada região. Cada implementação regional tem sua configuração exclusiva para os recursos, como a base de dados local.

A equipe de desenvolvimento está localizada em Seattle WA, utilizando o Datacenter E.U.A. oeste.

![Enviar para vários registos](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Enviar para vários registos*

Antes de utilizar as funcionalidades de georreplicação, a Contoso tinha um registo baseadas nos E.U.A. oeste dos e.u.a., com um registo adicional na Europa Ocidental. Para atender a esses diferentes regiões, a equipe de desenvolvimento enviados por push imagens aos dois registos diferentes.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Extrair a partir de vários registos](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Extrair a partir de vários registos*

Desafios típicos de vários registos incluem:

* Os clusters este dos E.U.A., oeste dos E.U.A. e Canadá Central todos os solicitar do registo E.U.A. oeste, as taxas de saída a incorrer em como cada um destes anfitriões de contentor remoto solicitar imagens do Oeste dos E.U.A. centros de dados.
* A equipe de desenvolvimento tem de enviar imagens para registos oeste dos E.U.A. e Europa Ocidental.
* A equipe de desenvolvimento tem de configurar e manter cada implementação regional com nomes de imagem que referencia o registro local.
* Acesso ao Registro deve ser configurado para cada região.

## <a name="benefits-of-geo-replication"></a>Benefícios da georreplicação

![Extrair a partir de um registo georreplicado](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Utilizar a funcionalidade de georreplicação do Azure Container Registry, esses benefícios são realizados:

* Gerir um registo único em todas as regiões: `contoso.azurecr.io`
* Gerir uma única configuração de implementações de imagens como todas as regiões utilizado o mesmo URL de imagem: `contoso.azurecr.io/public/products/web:1.2`
* Enviar para um único registo, enquanto ACR gerencia a replicação geográfica, incluindo regionais webhooks para notificações do locais

## <a name="configure-geo-replication"></a>Configurar georreplicação
Configurar georreplicação é tão fácil quanto clicar em regiões num mapa.

Replicação geográfica é uma funcionalidade do [registos Premium](container-registry-skus.md) apenas. Se o seu registo não estiver ainda Premium, pode alterar de básico e Standard para Premium no [portal do Azure](https://portal.azure.com):

![Mudar a SKUs no portal do Azure](media/container-registry-skus/update-registry-sku.png)

Para configurar a georreplicação para o seu registo Premium, inicie sessão no portal do Azure em https://portal.azure.com.

Navegue para o seu registo de contentor do Azure e selecione **replicações**:

![Replicações na IU de registo de contentor do portal do Azure](media/container-registry-geo-replication/registry-services.png)

É mostrado um mapa que mostra todas as regiões do Azure atual:

 ![Mapa de região no portal do Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Hexágonos azuis representam réplicas atuais
* Hexágonos verdes representam as regiões de réplica possível
* Hexágonos cinzentos representam as regiões do Azure ainda não está disponíveis para a replicação

Para configurar uma réplica, selecione um Hexágono verde, em seguida, selecione **criar**:

 ![Criar a IU de replicação no portal do Azure](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionais, selecione os Hexágonos verdes para outras regiões, em seguida, clique em **criar**.

ACR começa a sincronizar imagens entre as réplicas configuradas. Depois de concluído, o portal reflete *pronto*. O estado da réplica no portal não são atualizados automaticamente. Utilize o botão de atualização para ver o estado atualizado.

## <a name="geo-replication-pricing"></a>Preços de georreplicação

Replicação geográfica é uma funcionalidade dos [Premium SKU](container-registry-skus.md) do registo de contentor do Azure. Quando replicar um registo para as regiões desejadas, pode incorrer tarifas de registro de Premium para cada região.

No exemplo anterior, a Contoso consolidados dois registos de uma, adicionar réplicas para este dos E.U.A., Canadá Central e Europa Ocidental. Contoso pagaria Premium de quatro vezes por mês, sem qualquer configuração adicional ou gestão. Cada região agora obtém suas imagens localmente, melhorando o desempenho, confiabilidade, sem taxas de saída da rede do Oeste dos E.U.A., para o Canadá e E.U.A. Leste.

## <a name="summary"></a>Resumo

Com a georreplicação, pode gerenciar seus datacenters regionais como uma nuvem global. Como as imagens são utilizadas nos diversos recursos, pode beneficiar de um plano de gestão único mantendo perto da rede, rápida e extrai a imagem local confiável.

## <a name="next-steps"></a>Passos Seguintes

Confira a série de tutoriais de três partes, [georreplicação no Azure Container Registry](container-registry-tutorial-prepare-registry.md). Percorrer a criação de um registo georreplicado, criar um contentor e, em seguida, implantá-lo com um único `docker push` comando vários nas aplicações Web regionais para instâncias de contentores.

> [!div class="nextstepaction"]
> [Georreplicação no Azure Container Registry](container-registry-tutorial-prepare-registry.md)
