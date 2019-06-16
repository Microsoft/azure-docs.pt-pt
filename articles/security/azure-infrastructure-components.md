---
title: Componentes de sistema de informações do Azure e limites
description: Este artigo fornece uma descrição geral da arquitetura do Microsoft Azure e gestão.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587211"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes de sistema de informações do Azure e limites
Este artigo fornece uma descrição geral da arquitetura do Azure e gestão. O ambiente do sistema do Azure é constituído pelas redes seguintes:

- Rede de produção do Microsoft Azure (rede do Azure)
- Rede corporativa da Microsoft (corpnet)

As equipas de TI separado são responsáveis por operações e manutenção de uma destas redes.

## <a name="azure-architecture"></a>Arquitetura do Azure
O Azure é uma plataforma e infraestrutura para criar, implementar e gerir aplicações e serviços através de uma rede de datacenters de computação de nuvem. Microsoft gerencia esses datacenters. Com base no número de recursos que especificar, o Azure cria máquinas de virtuais (VMs) com base na necessidade de recursos. Estas VMs a executar num hipervisor do Azure, o que foi concebido para utilizar na cloud e não está acessíveis ao público.

Em cada nó de servidor físico do Azure, há um hipervisor que é executado diretamente sobre o hardware. O hipervisor divide um nó num número variável de máquinas virtuais convidadas. Cada nó também tem a raiz de uma VM, o que é executado o sistema operativo anfitrião. Firewall do Windows está ativada em cada VM. É possível definir as portas são endereçáveis ao configurar o ficheiro de definição de serviço. Estas portas são os únicos, abertos e endereçáveis, internamente ou externamente. Todos os tráfego e acesso a disco e rede é mediada pelo sistema de operativo hipervisor e raiz.

Na camada de anfitrião, as VMs do Azure, execute uma versão protegida e personalizada do Windows Server mais recente. O Azure utiliza uma versão do Windows Server que inclui apenas os componentes necessários para alojar VMs. Isto melhora o desempenho e reduz a superfície de ataque. Limites de máquina são impostos pelo hipervisor, que não depende da segurança do sistema operativo.

### <a name="azure-management-by-fabric-controllers"></a>Gestão do Azure por controladores de malha

No Azure, VMs em execução em servidores físicos (painéis/nós) são agrupadas em clusters de cerca de 1000. As VMs independentemente são geridas por um componente de software de plataforma expandidos e redundante chamado o controlador de malha (FC).

Cada FC gerencia o ciclo de vida das aplicações em execução no seu cluster e disposições e monitoriza o estado de funcionamento do hardware sob o seu controlo. Ele executa operações autonomic, como reincarnating instâncias de VMS em bom estado de funcionamento servidores quando ele determinar que um servidor falhou. O FC também executa operações de gestão de aplicações, como implementar, atualizar e ampliar aplicativos.

O datacenter é dividido em clusters. Clusters de isolar falhas ao nível do FC e impedem que determinadas classes de erros que afetam servidores além do cluster em que ocorrem. FCs que servem um determinado cluster do Azure são agrupadas num cluster FC.

### <a name="hardware-inventory"></a>Inventário de hardware

O FC prepara um inventário dos dispositivos de hardware e de rede do Azure durante o processo de configuração de arranque de configuração. Qualquer novo hardware e componentes de rede, introduzir o ambiente de produção do Azure tem de seguir o processo de configuração de arranque de configuração. O FC é responsável por gerenciar o inventário de todo listado no arquivo de configuração de datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Imagens de sistema de operativo geridos de FC

A equipe de sistema operativo fornece imagens, na forma de discos rígidos virtuais, implementado em todas as VMs de anfitrião e convidado no ambiente de produção do Azure. A equipe constrói essas imagens bases através de um processo automatizado de compilação offline. A imagem base é uma versão do sistema operativo em que o kernel e outros componentes centrais foram modificados e otimizados para suportar o ambiente do Azure.

Existem três tipos de imagens do sistema de operativo recursos de infraestrutura geridos:

- Host: Um sistema de operativo personalizado que é executado no anfitrião de VMs.
- Nativo: Um sistema operacional nativo que é executado em inquilinos (por exemplo, armazenamento do Azure). Este sistema operativo não tem qualquer hipervisor.
- Convidado: Um sistema operativo convidado que é executada em VMs de convidado.

O anfitrião e os sistemas de operativos geridos de FC nativos foram desenvolvidos para uso na nuvem e não estão acessíveis publicamente.

#### <a name="host-and-native-operating-systems"></a>Anfitriões e os sistemas de operativos nativos

Nativas e de anfitrião são imagens de sistema operacional fortalecidas que os agentes de recursos de infraestrutura de anfitrião e num nó de computação (executado como primeira VM no nó) e nós de armazenamento. A vantagem de usar imagens base otimizadas de host e nativo é que isso reduz a área de superfície exposta pelo APIs ou componentes não utilizados. Estes podem apresentar riscos de segurança elevados e aumentar a superfície do sistema operativo. Sistemas operativos de requisitos de espaço reduzido incluem apenas os componentes necessários para o Azure.

#### <a name="guest-operating-system"></a>Sistema operativo convidado

Componentes internos do Azure em execução em VMs do sistema de operativo convidado não tem nenhuma oportunidade de executar o protocolo de ambiente de trabalho remoto. Alterações às definições de configuração de linha de base tem de passar pela alteração e processo de gerenciamento de versão.

## <a name="azure-datacenters"></a>Datacenters do Azure
A equipe de infraestrutura de nuvem da Microsoft e de operações (MCIO) gerencia os recursos de infraestrutura e de datacenter físicos para todos os serviços online da Microsoft. MCIO é responsável principalmente para gerir os controles de físicos e ambientais datacenters, bem como gerenciamento e suporte a dispositivos de rede de perímetro externa (por exemplo, routers de limite e os routers de datacenter). MCIO também é responsável por configurar o hardware de servidor mínimo em racks no Centro de dados. Os clientes têm sem interação direta com o Azure.

## <a name="service-management-and-service-teams"></a>Gestão de serviço e as equipas de serviço
Vários grupos de engenharia, conhecidos como as equipas de serviço, faça a gestão de suporte do serviço do Azure. Cada equipe de serviço é responsável por uma área de suporte para o Azure. Cada equipe de serviço tem de disponibilizar um engenheiro disponível 24x7 para investigar e resolver falhas no serviço. As equipas de serviço não, por predefinição, tem acesso físico ao hardware a funcionar no Azure.

As equipes de serviço são:

- Plataforma de Aplicações
- Azure Active Directory
- Computação do Azure
- Azure Net
- Serviços de engenharia de cloud
- ISSD: Segurança
- Autenticação Multifator
- SQL Database
- Armazenamento

## <a name="types-of-users"></a>Tipos de usuários
Os funcionários (ou prestadores de serviços) da Microsoft são consideradas como os usuários internos. Todos os outros utilizadores são considerados como utilizadores externos. Todos os utilizadores internos do Azure têm o respetivo estado de funcionário categorizado com um nível de sensibilidade, que define o acesso aos dados dos clientes (acesso ou sem acesso). Privilégios de usuário para o Azure (permissão da autorização depois é feita a autenticação) são descritos na tabela a seguir:

| Função | Interno ou externo | Nível de sensibilidade | Privilégios autorizados e as funções executadas | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro de datacenter do Azure | Interno | Sem acesso aos dados dos clientes | Gerir a segurança física do local. Conduzir patrols dentro e fora do Centro de dados e monitorizar todos os pontos de entrada. Pessoal de ESCORT para dentro e fora do datacenter determinado não limpo que fornecem serviços gerais (como jantares ou limpeza) ou de trabalho IT no Centro de dados. Realize a rotina de monitorização e manutenção de hardware de rede. Realizar o trabalho de gestão e break-fix incidente utilizando uma variedade de ferramentas. Realize a monitorização e manutenção do hardware físico nos centros de dados de rotina. Acesso ao ambiente a pedido dos proprietários de propriedade. Com capacidade realizar investigações forenses, inicie sessão relatórios de incidentes e necessitam de requisitos de treinamento e a política de segurança obrigatória. Propriedade operacional e a manutenção das ferramentas de segurança críticas, como scanners e recolha de registos. | Acesso persistente para o ambiente. |
| Triagem de incidente do Azure (engenheiros de resposta rápida) | Interno | Acesso aos dados dos clientes | Faça a gestão de comunicações entre MCIO, suporte e equipas de engenharia. Triagem plataforma incidentes, problemas de implantação e pedidos de serviço. | Acesso just-in-time para o ambiente, com acesso persistente limitado a sistemas de não-clientes. |
| Engenheiros de implementação do Azure | Interno | Acesso aos dados dos clientes | Implementar e atualizar os componentes da plataforma, software e as alterações de configuração agendada para dar suporte do Azure. | Acesso just-in-time para o ambiente, com acesso persistente limitado a sistemas de não-clientes. |
| Suporte de indisponibilidade de cliente do Azure (inquilino) | Interno | Acesso aos dados dos clientes | Depurar e diagnosticar falhas de plataforma e de falhas para os inquilinos de computação individuais e as contas do Azure. Analise falhas. Unidade correções críticas para a plataforma ou o cliente e obtenha aprimoramentos técnicos em suporte. | Acesso just-in-time para o ambiente, com acesso persistente limitado a sistemas de não-clientes. |
| Os engenheiros de site em direto do Azure (monitorização engenheiros) e o incidente | Interno | Acesso aos dados dos clientes | Diagnosticar e mitigar o estado de funcionamento da plataforma, utilizando ferramentas de diagnóstico. Correções para controladores de volume da unidade, reparar itens resultantes de falhas e ajudá-lo em ações de restauro de indisponibilidade. | Acesso just-in-time para o ambiente, com acesso persistente limitado a sistemas de não-clientes. |
|Clientes do Azure | Externo | N/A | N/D | N/A |

O Azure utiliza os identificadores exclusivos para autenticar utilizadores organizacionais e os clientes (ou processos atuando em nome dos utilizadores organizacionais). Isto aplica-se a todos os recursos e dispositivos que fazem parte do ambiente do Azure.

### <a name="azure-internal-authentication"></a>Autenticação interna do Azure

Comunicações entre componentes internos do Azure estão protegidas com a encriptação TLS. Na maioria dos casos, os certificados X.509 são assinados automaticamente. Os certificados com ligações que podem ser acedidos a partir de fora da rede do Azure são uma exceção, pois são certificados para os FCs. FCs ter certificados emitidos por um Microsoft Certificate de autoridade (CA) que é apoiada por uma AC de raiz fidedigna. Isso permite que as chaves públicas de FC ser efetuado facilmente. Além disso, as ferramentas de programador do Microsoft utilizam as chaves públicas de FC. Quando os desenvolvedores submeter novas imagens de aplicação, as imagens são encriptadas com uma chave pública de FC para proteger segredos incorporados.

### <a name="azure-hardware-device-authentication"></a>Autenticação do dispositivo de hardware do Azure

O FC mantém um conjunto de credenciais (chaves e/ou as palavras-passe) utilizado para autenticar-se com vários dispositivos de hardware sob o seu controlo. A Microsoft utiliza um sistema para impedir o acesso a estas credenciais. Especificamente, o transporte, a persistência e a utilização destas credenciais foi concebido para impedir que os programadores do Azure, os administradores e acesso de serviços e a equipe de cópia de segurança informações confidenciais, confidenciais ou privadas.

A Microsoft utiliza encriptação com base na chave pública do FC identidade principal. Isto ocorre na configuração FC e tempos de reconfiguração de FC, para transferir as credenciais utilizadas para aceder a dispositivos de hardware de rede. Quando o FC tem as credenciais, o FC recupera e desencripta-os.

### <a name="network-devices"></a>Dispositivos de rede

A equipe de rede do Azure configura contas de serviço de rede para permitir que um cliente do Azure autenticar a dispositivos de rede (roteadores, comutadores e Balanceadores de carga).

## <a name="secure-service-administration"></a>Proteger a administração de serviços
O pessoal de operações do Azure é necessários para utilizar estações de trabalho de administrador seguras (SAWs). Os clientes podem implementar controlos semelhantes com estações de trabalho de acesso privilegiado. Com SAWs, o pessoal administrativo utiliza uma conta administrativa atribuída individualmente, que é separada da conta de usuário padrão do usuário. O SAW baseia-se essa prática de separação de conta, fornecendo uma estação de trabalho fidedigna para essas contas confidenciais.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
