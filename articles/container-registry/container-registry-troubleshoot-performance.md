---
title: Resolver problemas de desempenho de registo
description: Sintomas, causas e resolução de problemas comuns com o desempenho de um registo
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148412"
---
# <a name="troubleshoot-registry-performance"></a>Resolver problemas de desempenho de registo

Este artigo ajuda-o a resolver problemas que poderá encontrar com o desempenho de um registo de contentores Azure. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* Puxar ou empurrar imagens com o Docker CLI demora mais tempo do que o esperado
* A implementação de imagens para um serviço como o Serviço Azure Kubernetes demora mais tempo do que o esperado
* Não é capaz de completar um grande número de operações de puxão ou impulso simultâneas no tempo esperado
* As operações de puxar ou empurrar num registo geo-replicado demoram mais tempo do que o esperado, ou o impulso falha com erro `Error writing blob` ou `Error writing manifest`

## <a name="causes"></a>Causas

* A sua velocidade de ligação à rede pode atrasar as operações de registo - [solução](#check-expected-network-speed)
* Compressão ou extração da camada de imagem podem ser lentas no cliente - [solução](#check-client-hardware)  
* Está a atingir um limite configurado no seu nível de serviço de registo ou ambiente - [solução](#review-configured-limits)
* O seu registo geo-replicado tem réplicas em regiões próximas - [solução](#configure-geo-replicated-registry)
* Estás a retirar de uma réplica geograficamente distante do registo- [solução](#configure-dns-for-geo-replicated-registry)

Se não resolver o seu problema aqui, consulte [a resolução avançada de problemas](#advanced-troubleshooting) e [os próximos passos](#next-steps) para outras opções.

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="check-expected-network-speed"></a>Verifique a velocidade de rede esperada

Verifique a sua velocidade de upload e download na Internet, ou use uma ferramenta como a AzureSpeed para testar [o upload](https://www.azurespeed.com/Azure/Uploadß) e [o download](https://www.azurespeed.com/Azure/Download) do armazenamento de blob Azure, que acolhe camadas de imagem de registo.

Verifique o tamanho da sua imagem com o tamanho máximo suportado e o download suportado ou a largura de banda suportada para o seu nível de serviço de registo. Se o seu registo estiver no nível Básico ou Padrão, considere a atualização para melhorar o desempenho. 

Para a colocação de imagens noutros serviços, verifique as regiões onde o registo e o alvo estão localizados. Considere localizar o registo e o alvo de implantação nas mesmas regiões ou de proximidade de rede para melhorar o desempenho.

Links relacionados:

* [Níveis de serviço de registo de contentores Azure](container-registry-skus.md)    
* [FaQ do registo de contentores](container-registry-faq.md)
* [Objetivos de desempenho e escalabilidade para o armazenamento de blob Azure](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Verifique o hardware do cliente

O tipo de disco e CPU no cliente estivador pode afetar a velocidade de extrair ou comprimir camadas de imagem no cliente como parte de operações de puxar ou empurrar. Por exemplo, a extração de camadas num disco rígido demorará mais tempo do que num disco de estado sólido. Compare as operações de puxar para imagens comparáveis do seu registo de contentores Azure e um registo público como Docker Hub.

### <a name="review-configured-limits"></a>Rever limites configurados

Se estiver a empurrar ou a puxar várias ou muitas imagens em várias camadas para o seu registo, reveja os limites de ReadOps e WriteOps suportados para o nível de serviço de registo. Se o seu registo estiver no nível Básico ou Padrão, considere a atualização para aumentar os limites. Consulte também o seu fornecedor de rede sobre o estrangulamento da rede que pode ocorrer com muitas operações simultâneas. 

Reveja a sua configuração do Daemon Doemon do Docker para obter os carregamentos ou transferências simultâneas máximas para cada operação de push ou pull no cliente. Configure limites mais elevados, se necessário.

Como cada camada de imagem requer uma operação separada de leitura ou escrita, verifique o número de camadas nas suas imagens. Considere estratégias para reduzir o número de camadas de imagem.

Links relacionados:

* [Níveis de serviço de registo de contentores Azure](container-registry-skus.md)
* [estivado](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Registo geo-replicado configure

Um cliente Docker que empurra uma imagem para um registo geo-replicado pode não empurrar todas as camadas de imagem e o seu manifesto para uma única região replicada. Esta situação pode ocorrer porque o Azure Traffic Manager encaminha pedidos de registo para o registo replicado mais próximo da rede. Se o registo tiver duas regiões de replicação próximas, as camadas de imagem e o manifesto podem ser distribuídos pelos dois locais, e a operação de pressão falha quando o manifesto é validado.

Para otimizar a resolução de DNS à réplica mais próxima ao empurrar imagens, configurar um registo geo-replicado nas mesmas regiões do Azure como a fonte das operações push, ou a região mais próxima quando trabalha fora de Azure.

Para resolver problemas com um registo geo-replicado, também pode desativar temporariamente o encaminhamento do Gestor de Tráfego para uma ou mais replicações.

Links relacionados:

* [Geo-replicação no Registo do Contentor de Azure](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Configure DNS para registo geo-replicado

Se as operações de retirada de um registo geo-replicado parecerem lentas, a configuração de DNS no cliente poderá resolver-se para um servidor DNS geograficamente distante. Neste caso, o Traffic Manager pode estar a encaminhar pedidos para uma réplica que está próxima da rede do servidor DNS, mas distante do cliente. Executar uma ferramenta como `nslookup` ou `dig` (em Linux) para determinar a réplica a que o Gestor de Tráfego faz pedidos de registo. Por exemplo:

```console
nslookup myregistry.azurecr.io
```

Uma solução potencial é configurar um servidor DNS mais próximo.

Links relacionados:

* [Geo-replicação no Registo do Contentor de Azure](container-registry-geo-replication.md)
* [Operações de pressão de resolução de problemas com registos geo-replicados](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Desativar temporariamente o encaminhamento para a replicação](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Perguntas frequentes do gestor de tráfego](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Se as suas permissões para os recursos de registo permitirem, [verifique a saúde do ambiente de registo.](container-registry-check-health.md) Se os erros forem reportados, reveja a referência de [erro](container-registry-health-error-reference.md) para soluções potenciais.

Se a [recolha de registos de recursos](container-registry-diagnostics-audit-logs.md) estiver ativada no registo, reveja o registo DeTerRegistryRepositoryEvents. Este registo armazena informações para operações como eventos de empurrar ou puxar. Consultar o registo de [falhas de funcionamento ao nível do repositório](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Links relacionados:

* [Registos para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)
* [FaQ do registo de contentores](container-registry-faq.md)
* [Melhores práticas do Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

Se não resolver o seu problema aqui, consulte as seguintes opções.

* Outros tópicos de resolução de problemas do registo incluem:
  * [Login de registo de resolução de problemas](container-registry-troubleshoot-login.md)
  * [Problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md)
* [Opções de apoio comunitário](https://azure.microsoft.com/support/community/)
* [Perguntas e Respostas da Microsoft](/answers/products/)
* [Abrir um pedido de suporte](https://azure.microsoft.com/support/create-ticket/)