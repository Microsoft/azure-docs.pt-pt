---
title: Visão geral do provisionamento do Linux
description: Visão geral de como trazer as suas imagens Linux VM ou criar novas imagens para usar em Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563223"
---
# <a name="azure-linux-vm-provisioning"></a>Provisão de Azure Linux VM
Quando cria um VM a partir de uma imagem generalizada (Galeria de Imagens Partilhada ou Imagem Gerida), o plano de controlo permitir-lhe-á criar um VM e passar parâmetros e configurações para o VM. Isto chama-se *provisão* VM . Durante o fornecimento, a plataforma disponibiliza os valores de parâmetros VM Create (nome de anfitrião, nome de utilizador, palavra-passe, teclas SSH, customData) disponíveis para o VM à medida que arranca. 

Um agente de provisionamento assado dentro da imagem irá interagir com a plataforma, conectando-se a várias interfaces independentes de provisionamento), definir as propriedades e sinal para a plataforma que completou. 

Os agentes de provisionamento podem ser o [Agente Azure Linux,](../extensions/agent-linux.md)ou [cloud-init](./using-cloud-init.md). Estes são [pré-requisitos](create-upload-generic.md) para a criação de imagens generalizadas.

Os agentes de provisionamento, fornecem suporte para todas as [distribuições Azure Linux endossadas](./endorsed-distros.md), e você encontrará as imagens de distro endossadas em muitos casos irá enviar com cloud-init e o Linux Agent. Isto dá-lhe a opção de ter uma ineção em nuvem para lidar com o fornecimento, então o Agente Linux fornecerá suporte para lidar com [extensões Azure](../extensions/features-windows.md). Fornecer suporte para extensões significa que o VM será então elegível para suportar serviços adicionais de Azure, tais como VM Password Reset, Azure Monitoring, Azure Backup, Encriptação do Disco Azure, etc.

Após o provisionamento completo, a cloud-init será executada em cada bota. A cloud-init irá monitorizar alterações no VM, como alterações de rede, montagem e formatação do disco efémero, e iniciar o Agente Linux. O Agente Linux funciona continuamente no servidor, procurando um 'goal state' (nova configuração) a partir da plataforma Azure, pelo que sempre que instalar extensões, o agente poderá processá-las.

Embora existam atualmente dois agentes de provisionamento, o cloud-in deve ser o agente de provisionamento que escolher, e o Agente Linux deve ser instalado para suporte de extensão. Isto permite-lhe tirar partido das otimizações da plataforma, e permite-lhe desativar/remover o Agente Linux, para obter mais detalhes sobre como criar imagens sem o agente, e como removê-las, por favor reveja esta [documentação.](disable-provisioning.md)

Se tem um kernel Linux que não suporta executar nenhum dos agentes, mas deseja definir algumas das propriedades VM Create, como nome de anfitrião, customData, userName, password, ssh keys, então neste documento discute como pode [criar imagens generalizadas sem um agente,](no-agent.md)e cumprir os requisitos da plataforma.


## <a name="provisioning-agent-responsibilities"></a>Prestação de responsabilidades por agente

**Provisão de imagem**
  
- Criação de uma conta de utilizador
- Configurar tipos de autenticação SSH
- Implantação de chaves públicas SSH e pares de chaves
- Definição do nome do anfitrião
- Publicar o nome de anfitrião para a plataforma DNS
- Relatório SSH anfitrião chave impressão digital chave para a plataforma
- Gestão de Discos de Recursos
- Formatação e montagem do disco de recursos
- Consumir e processar `customData`
 
**Rede**
  
- Gere rotas para melhorar a compatibilidade com os servidores DHCP da plataforma
- Garante a estabilidade do nome da interface de rede

**Kernel**
  
- Configura UMA virtual (desativação para <de kernel `2.6.37` )
- Consome entropia Hiper-V para `/dev/random`
- Configura os intervalos de tempo SCSI para o dispositivo radicial (que pode ser remoto)

**Diagnóstico**
  
- Redirecionamento da consola para a porta em série

## <a name="communication"></a>Comunicação
O fluxo de informação da plataforma para o agente ocorre através de dois canais:

- Um DVD anexado para implantações iaaS. O DVD inclui um ficheiro de configuração compatível com OVF que inclui todas as informações de fornecimento, para além dos pares de chaves SSH reais.
- Um ponto final TCP expondo uma API REST usada para obter implantação e configuração de topologia.


## <a name="azure-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento Azure
O Agente Linux, e o cloud-init, dependem de alguns pacotes do sistema para funcionar corretamente:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Utilitários do sistema de ficheiros: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Ferramentas de senha: chpasswd, sudo
- Ferramentas de processamento de texto: sed, grep
- Ferramentas de rede: via ip
- Suporte kernel para a montagem de sistemas de ficheiros UDF.

## <a name="next-steps"></a>Passos seguintes

Se for necessário, pode [desativar o provisionamento e remover o agente Linux](disable-provisioning.md).
