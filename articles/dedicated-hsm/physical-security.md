---
title: Segurança física do HSM - HSM dedicada do Azure | Documentos da Microsoft
description: Informações sobre a segurança física dos dispositivos de HSM dedicados do Azure nos centros de dados
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 6fe0557aa90ec3345d4ca0c3d9292cab3435bb2f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118065"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Segurança física de HSM dedicados do Azure

HSM dedicada do Azure ajuda-o a cumprir os requisitos de segurança avançadas para o armazenamento de chaves. É gerenciado seguintes práticas de segurança rigorosas em todo o ciclo completo para atender às necessidades dos clientes.

## <a name="security-through-procurement"></a>Segurança através de aprovisionamento

Microsoft segue um processo de aprovisionamento seguro. Podemos gerir a cadeia de custódia e certifique-se de que o dispositivo específico ordenadas e fornecido é o dispositivo que chegam ao nosso centros de dados. Os dispositivos estão em faz uma cópia de plástico do evento de adulteração. Eles são armazenados numa área de armazenamento seguro até que empregasse na Galeria de dados do Centro de dados.  Os racks que contém os dispositivos HSM são considerados impact(HBI) comerciais altos. Os dispositivos estão bloqueados e em vigilância de vídeo, o tempo todo front-end e back.

## <a name="security-through-deployment"></a>Segurança por meio da implantação

HSMs são instalados em racks, juntamente com os componentes de rede associados. Uma vez instalado, têm de ser configuradas antes de estar disponíveis como parte integrante do serviço de HSM do Azure dedicado. Esta atividade de configuração é realizada por funcionários da Microsoft que já passaram por uma verificação de segundo plano. "Just-In-Time" administração (JIT) é utilizada para limitar o acesso aos funcionários apenas certos e apenas o tempo que for necessário acesso. Os procedimentos e sistemas utilizados também garantem que todas as atividades relacionadas com os dispositivos HSM está registada.

## <a name="security-in-operations"></a>Segurança em operações

HSMs são dispositivos de hardware (o HSM real que está a ser um cartão PCI dentro da aplicação), portanto, é possível que possam surgir problemas de nível de componente. Problemas potenciais incluem, mas não estão limitados a ventoinha e power falhas de fonte de alimentação. Este tipo de evento será exigem manutenção ou reparação e atividades para substituir quaisquer componentes-swap.

### <a name="component-replacement"></a>Substituição de componente

Depois de um dispositivo é aprovisionado e sob a gestão de clientes, a fontes de alimentação é apenas componentes que teriam de ser substituídos. Este componente está fora do limite de segurança e não faz com que um evento de adulteração. Um sistema de permissões é utilizado para autorizar um engenheiro da Microsoft para aceder a atrás do HBI rack. Quando o pedido de suporte é processado uma chave temporária de física é emitida. Esta chave permite o acesso de engenheiro no dispositivo e permite-lhes trocar o componente afetado. Qualquer outro tipo de acesso (ou seja, adulteração evento causar) teria de ser feito quando um dispositivo não está atribuído a um cliente, portanto, minimizando o risco de segurança e a disponibilidade.  

### <a name="device-replacement"></a>Substituição de dispositivo

Em caso de falha total de dispositivos, é seguido um processo semelhante à utilizada durante uma falha de componente. Se um cliente não é capaz de zeroize o dispositivo ou o dispositivo está num Estado desconhecido, os dados tendo dispositivos serão removidos e colocados numa posição de destruição em rack. Serão destruídos colocados no contentor de dispositivos de forma controlada e segura. Não existem dados tendo dispositivos a partir de um HBI rack deixar um datacenter da Microsoft.

### <a name="other-rack-access-activities"></a>Outras atividades de acesso de bastidor

Se o bastidor utilizado pelos dispositivos HSM (por exemplo, manutenção funcionamento em rede do dispositivo) têm de aceder a um engenheiro da Microsoft, procedimentos de segurança padrão serão utilizados para obter acesso ao HBI rack seguro. Todos os acessos encontrar-se-irão em vídeo vigilância. Os dispositivos HSM sejam validados [FIPS 140-2 nível 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) para que qualquer acesso não autorizado aos dispositivos HSM irão ser indicado para o cliente e irão ser zeroized dados.

## <a name="logical-level-security-considerations"></a>Considerações de segurança de nível lógico

HSMs são aprovisionados para uma rede virtual criada pelo cliente. Este é o espaço de endereços de IUP privado de um cliente.  Esta configuração fornece um isolamento em nível de rede lógica valiosas e garante que apenas o acesso do cliente. Isso implica que todos os controles de segurança ao nível lógico são de responsabilidade do cliente.

## <a name="next-steps"></a>Passos Seguintes

Recomenda-se que todos os principais conceitos do serviço, como elevada disponibilidade e a segurança e a capacidade de suporte por exemplo, são bem compreendidos antes de aprovisionamento de dispositivos, design do aplicativo ou a implementação.

* [Elevada disponibilidade](high-availability.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)
* [Arquitetura de implantação](deployment-architecture.md)