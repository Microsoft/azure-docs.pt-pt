---
title: Matriz de suporte de migrações para Azure para avaliação e migração de servidor físico
description: Resume as configurações e limitações para avaliação e migração de servidor físico usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 56e49ab60733c9c0a813ca6e46712bcd8052a2ef
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518154"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Matriz de suporte para avaliação e migração de servidor físico

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar servidores físicos locais.


## <a name="physical-server-scenarios"></a>Cenários de servidor físico

A tabela resume os cenários com suporte para servidores físicos.

**Implementação** | **Detalhes***
--- | ---
**Avaliar servidores físicos locais** | [Configure](tutorial-prepare-physical.md) sua primeira avaliação.
**Migrar servidores físicos para o Azure** | [Experimente](tutorial-migrate-physical-virtual-machines.md) a migração para o Azure.


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
**Permissões do Azure** | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
**Servidores físicos** | Avalie até 250 servidores físicos em um único projeto. Você pode ter vários projetos em uma assinatura do Azure. Um projeto pode incluir servidores físicos, VMs VMware e VMs do Hyper-V, até os limites de avaliação.
**Geografia** | Você pode criar projetos de migrações para Azure em uma série de geografias. Embora seja possível criar projetos em geografias específicos, você pode avaliar ou migrar computadores para outros locais de destino. A geografia do projeto é usada somente para armazenar os metadados descobertos.

  **Geografia** | **Local de armazenamento de metadados**
  --- | ---
  Azure Government | Gov (US) - Virginia
  Ásia-Pacífico | Ásia Oriental ou sudeste asiático
  Austrália | Leste da Austrália ou sudeste da Austrália
  Brasil | Sul do Brasil
  Canadá | Canadá central ou leste do Canadá
  Europa | Europa Setentrional ou Europa Ocidental
  França | França Central
  Índia | Índia central ou sul da Índia
  Japão |  Leste do Japão ou oeste do Japão
  Coreia | Coreia central ou sul da Coreia
  Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
  Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.


## <a name="assessment-physical-server-requirements"></a>Avaliação-requisitos do servidor físico

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de servidor físico**       | O servidor físico pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | **Windows:** Configure uma conta de usuário local em todos os servidores Windows que você deseja incluir na descoberta. A conta de usuário precisa ser adicionada a esses grupos-Área de Trabalho Remota usuários, usuários do monitor de desempenho e usuários de log de desempenho. <br/> **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir. |
| **Sistema operativo** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |


## <a name="assessment-appliance-requirements"></a>Avaliação – requisitos de dispositivo

Para avaliação, as migrações para Azure executam um dispositivo leve para descobrir servidores físicos e enviar metadados de servidor e dados de desempenho para migrações para Azure. O dispositivo pode ser executado em um servidor físico ou em uma VM e você o configura usando um script do PowerShell que você baixa do portal do Azure. A tabela a seguir resume os requisitos do dispositivo.

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de dispositivo**   |  Você pode implantar o dispositivo em um servidor físico ou VM.<br/>  O computador host deve estar executando o Windows Server 2012 R2 ou posterior.<br/> O host precisa de espaço suficiente para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e acesso à Internet.
| **Projeto de migrações para Azure**  |  Um dispositivo pode ser associado a um único projeto.<br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> Você pode avaliar até 35.000 computadores em um projeto.
| **Descoberta**              | Um único dispositivo pode descobrir até 200 servidores.
| **Grupo de avaliação**       | Você pode adicionar até 35.000 computadores em um único grupo.
| **Avaliação**             | Você pode avaliar até 35.000 computadores em uma única avaliação.


## <a name="assessment-appliance-url-access"></a>Avaliação – acesso à URL do dispositivo

Para avaliar as VMs, o dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL, permita o acesso às URLs na tabela, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.
- Se você tiver um proxy de interceptação, talvez seja necessário importar o certificado do servidor do servidor proxy para o dispositivo.


**URL** | **Detalhes**  
--- | ---
*.portal.azure.com | Navegação para a portal do Azure
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Inicie sessão na sua subscrição do Azure
*.microsoftonline.com <br/> *. microsoftonline-p.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
management.azure.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
dc.services.visualstudio.com | Início de sessão e monitorização
*.vault.azure.net | Gerencie segredos em Azure Key Vault ao se comunicar entre o dispositivo e o serviço.
aka.ms/* | Permitir acesso a links conhecidos.
https://download.microsoft.com/download/* | Permite downloads do site de download da Microsoft.



## <a name="assessment-port-requirements"></a>Avaliação – requisitos de porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Vice** | **Conexão**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Conexões de saída nas portas 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
**Servidores físicos** | **Windows:** Conexões de entrada nas portas 443, 5989 para efetuar pull de metadados de configuração e desempenho de servidores Windows. <br/> **Linux:**  Conexões de entrada na porta 22 (UDP) para efetuar pull de metadados de configuração e desempenho de servidores Linux. |


## <a name="next-steps"></a>Passos seguintes

[Prepare-se para a avaliação do servidor físico](tutorial-prepare-physical.md) para avaliação e migração de servidor físico.
