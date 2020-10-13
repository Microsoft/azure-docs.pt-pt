---
title: Visão geral da criação de imagens Linux para Azure
description: Como trazer as suas imagens Linux VM ou criar novas imagens para usar no Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 61c21aed76cfaac5621b234b32c90877ef6faa9f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966326"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Trazer e criar imagens Linux em Azure

Esta visão geral abrange os conceitos básicos em torno da imagem e como construir e usar com sucesso imagens Linux em Azure. Antes de levar uma imagem personalizada ao Azure, tem de estar atento aos tipos e opções disponíveis.

Este artigo falará através dos pontos e requisitos de decisão de imagem, explicará conceitos-chave, para que possa seguir este artigo e ser capaz de criar as suas próprias imagens personalizadas à sua especificação.

## <a name="difference-between-managed-disks-and-images"></a>Diferença entre discos geridos e imagens


O Azure permite-lhe trazer um VHD para a plataforma, para usar como [disco gerido,](../faq-for-disks.md#managed-disks)ou usar como fonte para uma imagem. 

Os discos geridos Azure são VHDs únicos. Pode pegar num VHD existente e criar um disco gerido a partir dele, ou criar um disco gerido vazio a partir do zero. Pode criar VMs a partir de discos geridos, fixando o disco ao VM, mas só pode utilizar um VHD com um VM. Não é possível modificar nenhuma propriedade de SO, o Azure tentará apenas ligar o VM e iniciar a utilização desse disco. 

As imagens Azure podem ser compostas por vários discos de SO e discos de dados. Quando utiliza uma imagem gerida para criar um VM, a plataforma faz uma cópia da imagem e usa-a para criar o VM, pelo que o suporte de imagem gerido reutiliza a mesma imagem para vários VMs. O Azure também fornece capacidades avançadas de gestão para imagens, como a replicação global, e a versão através [da Shared Image Gallery.](shared-image-galleries.md) 



## <a name="generalized-and-specialized"></a>Generalizada e especializada

O Azure oferece dois tipos principais de imagem, generalizados e especializados. Os termos generalizados e especializados são originalmente termos Windows, que migraram para Azure. Estes tipos definem como a plataforma irá lidar com o VM quando o liga. Ambos os tipos têm vantagens e desvantagens, e pré-requisitos. Antes de começar, precisa saber de que tipo de imagem vai precisar. Abaixo resume os cenários e o tipo que você precisaria escolher:

| Cenário      | Tipo de imagem  | Opções de armazenamento |
| ------------- |:-------------:| :-------------:| 
| Crie uma imagem que possa ser configurada para utilização por vários VMs, e posso definir o nome de anfitrião, adicionar um utilizador administrativo e executar outras tarefas durante a primeira bota. | Generalizada | Galeria de Imagens Partilhadas ou imagens geridas autónomas |
| Crie uma imagem a partir de um instantâneo VM, ou uma cópia de segurança | Especializada |Galeria de Imagens Partilhadas ou um disco gerido |
| Crie rapidamente uma imagem que não precise de nenhuma configuração para criar vários VMs |Especializada |Galeria de Imagens Partilhada |


### <a name="generalized-images"></a>Imagens generalizadas

Uma imagem generalizada é uma imagem que requer que a configuração seja concluída na primeira bota. Por exemplo, na primeira bota, definiu o nome de anfitrião, o utilizador administrativo e outras configurações específicas de VM. Isto é útil quando se quer que a imagem seja reutilizada várias vezes, e quando pretende passar em parâmetros durante a criação. Se a imagem generalizada contiver o agente Azure, o agente processará os parâmetros e sinalizará de volta para a plataforma que a configuração inicial completou. Este processo chama-se [provisionamento.](./provisioning.md) 

O provisionamento requer que um provisionador seja incluído na imagem. Há dois provisionadores:
- [Agente Azure Linux](../extensions/agent-linux.md)
- [nuvem-init](./using-cloud-init.md)

Estes são [pré-requisitos](./create-upload-generic.md) para criar uma imagem.


### <a name="specialized-images"></a>Imagens especializadas
Estas são imagens que estão completamente configuradas e não requerem VM e parâmetros especiais, a plataforma vai apenas ligar o VM, você precisa lidar com a singularidade dentro do VM, como definir um nome de hospedeiro, para evitar conflitos DNS no mesmo VNET. 

Os agentes de provisionamento não são necessários para estas imagens, no entanto, é melhor que seja necessário ter capacidades de tratamento de extensões. Pode instalar o Agente Linux, mas desativar a opção de provisionamento. Mesmo que não precise de um agente de provisionamento, a imagem deve cumprir [os requisitos pré-requisitos](./create-upload-generic.md)  para a Azure Images.


## <a name="image-storage-options"></a>Opções de armazenamento de imagem
Ao trazer a sua imagem Linux, tem duas opções:

- Imagens geridas para uma simples criação de VM num ambiente de desenvolvimento e teste.
- [Galeria de Imagens Partilhada](shared-image-galleries.md) para criar e partilhar imagens em escala.


### <a name="managed-images"></a>Imagens geridas

Imagens geridas podem ser usadas para criar vários VMs, mas têm muitas limitações. As imagens geridas só podem ser criadas a partir de uma fonte generalizada (VM ou VHD). Só podem ser usados para criar VMs na mesma região e não podem ser partilhados entre assinaturas e inquilinos.

Imagens geridas podem ser usadas para ambientes de desenvolvimento e teste, onde você precisa de um par de imagens generalizadas simples para usar dentro de uma única região e subscrição. 

### <a name="azure-shared-image-gallery-sig"></a>Galeria de Imagens Partilhadas Azure (SIG)

[As Galerias de Imagem Partilhada](shared-image-galleries.md) são recomendadas para criar, gerir e partilhar imagens em escala. As galerias de imagens partilhadas ajudam-no a construir estrutura e organização em torno das suas imagens.  

- Apoio a imagens generalizadas e especializadas.
- Suporte para imagem tanto de geração 1 como de 2 imagens.
- Replicação global de imagens.
- Versão e agrupamento de imagens para uma gestão mais fácil.
- Imagens altamente disponíveis com Zone Redundant Storage (ZRS), em regiões que suportam Zonas de Disponibilidade. O ZRS oferece uma melhor resiliência contra falhas zonais.
- Partilha entre subscrições, e até entre inquilinos do Ative Directory (AD), utilizando o RBAC.
- Escalar as suas implementações com réplicas de imagem em cada região.

A um nível elevado, cria-se um SIG, e é composto por:
- Definições de Imagem - Estes são recipientes que detêm grupos de imagens.
- Versões de imagem - Estas são as imagens reais



## <a name="hyper-v-generation"></a>Geração Hiper-V

Azure suporta Hiper-V Geração 1 (Gen1) e Geração 2 (Gen2), a Gen2 é a última geração, e oferece funcionalidades adicionais sobre a Gen1. Por exemplo: memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM). Os VMs de geração 2 que estão no local, têm algumas funcionalidades que ainda não foram suportadas no Azure. Para mais informações, consulte a secção Funcionalidades e Capacidades. Para mais informações consulte este [artigo.](../generation-2.md) Crie imagens Gen2 se necessitar da funcionalidade adicional.

Se ainda precisar de criar a sua própria imagem, certifique-se de que cumpre os [pré-requisitos](./create-upload-generic.md)de imagem e faça o upload para Azure. Requisitos específicos de distribuição:


- [Distribuições baseadas em CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Passos seguintes

Saiba como criar uma [Galeria de Imagens Partilhadas.](tutorial-custom-images.md)