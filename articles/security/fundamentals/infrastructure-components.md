---
title: Componentes e limites do sistema de informações do Azure
description: Este artigo fornece uma descrição geral da arquitetura e do gerenciamento de Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727200"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes e limites do sistema de informações do Azure
Este artigo fornece uma descrição geral da arquitetura e do gerenciamento do Azure. O ambiente do sistema do Azure é composto pelas seguintes redes:

- Rede de produção Microsoft Azure (rede do Azure)
- Rede corporativa da Microsoft (corpnet)

Equipes de ti separadas são responsáveis por operações e manutenção dessas redes.

## <a name="azure-architecture"></a>Arquitetura do Azure
O Azure é uma plataforma de computação em nuvem e uma infraestrutura para criação, implantação e gerenciamento de aplicativos e serviços por meio de uma rede de data centers. A Microsoft gerencia esses data centers. Com base no número de recursos que você especificar, o Azure cria VMs (máquinas virtuais) com base na necessidade de recursos. Essas VMs são executadas em um hipervisor do Azure, que é projetado para uso na nuvem e não pode ser acessado pelo público.

Em cada nó do servidor físico do Azure, há um hipervisor que é executado diretamente no hardware. O hipervisor divide um nó em um número variável de VMs convidadas. Cada nó também tem uma VM raiz, que executa o sistema operacional do host. O Firewall do Windows está habilitado em cada VM. Você define quais portas são endereçáveis Configurando o arquivo de definição de serviço. Essas portas são as únicas abertas e endereçáveis, interna ou externamente. Todo o tráfego e acesso ao disco e à rede é corrigido pelo hipervisor e pelo sistema operacional raiz.

Na camada de host, as VMs do Azure executam uma versão personalizada e protegida do Windows Server mais recente. O Azure usa uma versão do Windows Server que inclui somente os componentes necessários para hospedar VMs. Isso melhora o desempenho e reduz a superfície de ataque. Os limites de máquina são impostos pelo hipervisor, o que não depende da segurança do sistema operacional.

### <a name="azure-management-by-fabric-controllers"></a>Gerenciamento do Azure por controladores de malha

No Azure, as VMs em execução em servidores físicos (Blades/nós) são agrupadas em clusters de aproximadamente 1000. As VMs são gerenciadas independentemente por um componente de software de plataforma redundante e expandido chamado de controlador de malha (FC).

Cada FC gerencia o ciclo de vida dos aplicativos em execução em seu cluster e provisiona e monitora a integridade do hardware sob seu controle. Ele executa operações de autonomia, como instâncias de VM reincarnating em servidores íntegros quando determina que um servidor falhou. O FC também executa operações de gerenciamento de aplicativos, como implantação, atualização e dimensionamento de aplicativos.

O datacenter é dividido em clusters. Os clusters isolam as falhas no nível do FC e impedem que determinadas classes de erros afetem os servidores além do cluster no qual ocorrem. O FCs que atende a um cluster específico do Azure é agrupado em um cluster FC.

### <a name="hardware-inventory"></a>Inventário de hardware

O FC prepara um inventário de dispositivos de rede e hardware do Azure durante o processo de configuração de inicialização. Todos os novos componentes de hardware e de rede que entram no ambiente de produção do Azure devem seguir o processo de configuração de inicialização. O FC é responsável por gerenciar todo o inventário listado no arquivo de configuração do datacenter. xml.

### <a name="fc-managed-operating-system-images"></a>Imagens do sistema operacional gerenciado por FC

A equipe do sistema operacional fornece imagens, na forma de discos rígidos virtuais, implantadas em todas as VMs de host e convidado no ambiente de produção do Azure. A equipe constrói essas imagens base por meio de um processo de compilação offline automatizado. A imagem base é uma versão do sistema operacional em que o kernel e outros componentes principais foram modificados e otimizados para dar suporte ao ambiente do Azure.

Há três tipos de imagens do sistema operacional gerenciadas por malha:

- Hospedeira Um sistema operacional personalizado que é executado em VMs de host.
- Forma Um sistema operacional nativo que é executado em locatários (por exemplo, armazenamento do Azure). Este sistema operacional não tem nenhum hipervisor.
- Convite Um sistema operacional convidado que é executado em VMs convidadas.

O host e os sistemas operacionais gerenciados por FC nativo são projetados para uso na nuvem e não são acessíveis publicamente.

#### <a name="host-and-native-operating-systems"></a>Host e sistemas operacionais nativos

Host e nativo são imagens de sistema operacional protegidas que hospedam os agentes de malha e são executadas em um nó de computação (executado como primeira VM no nó) e nós de armazenamento. O benefício de usar imagens base otimizadas do host e nativo é que ela reduz a área de superfície exposta por APIs ou componentes não utilizados. Eles podem apresentar riscos de alta segurança e aumentar a superfície do sistema operacional. Os sistemas operacionais de superfície reduzida incluem apenas os componentes necessários para o Azure.

#### <a name="guest-operating-system"></a>Sistema operacional convidado

Os componentes internos do Azure executados em VMs do sistema operacional convidado não têm oportunidade de executar protocolo RDP. As alterações nas definições de configuração de linha de base devem passar pelo processo de gerenciamento de alterações e liberações.

## <a name="azure-datacenters"></a>Datacenters do Azure
A equipe de Microsoft Cloud infraestrutura e operações (MCIO) gerencia a infraestrutura física e as instalações de datacenter para todos os serviços online da Microsoft. O MCIO é responsável principalmente pelo gerenciamento dos controles físicos e ambientais dentro dos data centers, bem como pelo gerenciamento e suporte de dispositivos de rede de perímetro externos (como roteadores de borda e roteadores de Datacenter). O MCIO também é responsável por configurar o hardware mínimo de servidor em racks no datacenter. Os clientes não têm interação direta com o Azure.

## <a name="service-management-and-service-teams"></a>Gerenciamento de serviços e equipes de serviço
Vários grupos de engenharia, conhecidos como equipes de serviço, gerenciam o suporte do serviço do Azure. Cada equipe de serviço é responsável por uma área de suporte para o Azure. Cada equipe de serviço deve tornar um engenheiro disponível 24x7 para investigar e resolver falhas no serviço. As equipes de serviço não, por padrão, têm acesso físico ao hardware operando no Azure.

As equipes de serviço são:

- Plataforma de Aplicações
- Azure Active Directory
- Computação do Azure
- Rede do Azure
- Serviços de engenharia de nuvem
- ISSD: Segurança
- Autenticação Multifator
- SQL Database
- Armazenamento

## <a name="types-of-users"></a>Tipos de usuários
Os funcionários (ou contratados) da Microsoft são considerados usuários internos. Todos os outros usuários são considerados usuários externos. Todos os usuários internos do Azure têm seu status de funcionário categorizado com um nível de sensibilidade que define o acesso aos dados do cliente (acesso ou sem acesso). Os privilégios de usuário para o Azure (permissão de autorização após a autenticação ocorrem) são descritos na tabela a seguir:

| Role | Interno ou externo | Nível de sensibilidade | Privilégios e funções autorizados executados | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro de datacenter do Azure | Interna | Sem acesso aos dados do cliente | Gerencie a segurança física do local. Realize os patrulhas dentro e fora do datacenter e monitore todos os pontos de entrada. Escort para dentro e fora do datacenter determinadas pessoas não limpas que fornecem serviços gerais (como jantar ou limpeza) ou trabalham no datacenter. Realize monitoramento de rotina e manutenção de hardware de rede. Execute o gerenciamento de incidentes e o trabalho de conserto e correção usando uma variedade de ferramentas. Realize o monitoramento e a manutenção de rotina do hardware físico nos data centers. Acesso ao ambiente sob demanda de proprietários de propriedade. Capaz de realizar investigações forenses, registrar relatórios de incidentes e exigir requisitos de política e treinamento de segurança obrigatórios. Propriedade operacional e manutenção de ferramentas de segurança críticas, como scanners e coleta de log. | Acesso persistente ao ambiente. |
| Triagem de incidentes do Azure (engenheiros de resposta rápidos) | Interna | Acesso aos dados do cliente | Gerencie comunicações entre equipes de MCIO, suporte e engenharia. Triagem de incidentes de plataforma, problemas de implantação e solicitações de serviço. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas que não são clientes. |
| Engenheiros de implantação do Azure | Interna | Acesso aos dados do cliente | Implante e atualize componentes de plataforma, software e alterações de configuração agendadas para dar suporte ao Azure. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas que não são clientes. |
| Suporte de interrupção do cliente do Azure (locatário) | Interna | Acesso aos dados do cliente | Depure e diagnostique interrupções de plataforma e falhas para locatários de computação individuais e contas do Azure. Analise as falhas. Gere correções críticas para a plataforma ou o cliente e gere melhorias técnicas no suporte. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas que não são clientes. |
| Engenheiros de sites do Azure Live (engenheiros de monitoramento) e incidentes | Interna | Acesso aos dados do cliente | Diagnostique e reduza a integridade da plataforma usando ferramentas de diagnóstico. Fixe as correções para drivers de volume, repare os itens resultantes de interrupções e ajude as ações de restauração de interrupção. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas que não são clientes. |
|Clientes do Azure | Externo | N/A | N/D | N/A |

O Azure usa identificadores exclusivos para autenticar usuários e clientes organizacionais (ou processos agindo em nome de usuários organizacionais). Isso se aplica a todos os ativos e dispositivos que fazem parte do ambiente do Azure.

### <a name="azure-internal-authentication"></a>Autenticação interna do Azure

As comunicações entre os componentes internos do Azure são protegidas com criptografia TLS. Na maioria dos casos, os certificados X. 509 são autoassinados. Certificados com conexões que podem ser acessadas de fora da rede do Azure são uma exceção, assim como certificados para o FCs. O FCs tem certificados emitidos por um certificado de autoridade de certificação (CA) da Microsoft que é apoiado por uma autoridade de certificação raiz confiável. Isso permite que as chaves públicas do FC sejam transferidas com facilidade. Além disso, as ferramentas de desenvolvedor da Microsoft usam chaves públicas do FC. Quando os desenvolvedores enviam novas imagens de aplicativos, as imagens são criptografadas com uma chave pública FC para proteger quaisquer segredos inseridos.

### <a name="azure-hardware-device-authentication"></a>Autenticação de dispositivo de hardware do Azure

O FC mantém um conjunto de credenciais (chaves e/ou senhas) usado para se autenticar em vários dispositivos de hardware sob seu controle. A Microsoft usa um sistema para impedir o acesso a essas credenciais. Especificamente, o transporte, a persistência e o uso dessas credenciais foram projetados para impedir que os desenvolvedores, os administradores e os serviços de backup e o acesso à equipe do Azure acessem informações confidenciais, confidenciais ou privadas.

A Microsoft usa a criptografia com base na chave pública de identidade mestre do FC. Isso ocorre na configuração do FC e nos tempos de reconfiguração de FC, para transferir as credenciais usadas para acessar dispositivos de hardware de rede. Quando o FC precisa das credenciais, o FC as recupera e descriptografa.

### <a name="network-devices"></a>Dispositivos de rede

A equipe de rede do Azure configura as contas de serviço de rede para permitir que um cliente do Azure se autentique em dispositivos de rede (roteadores, comutadores e balanceadores de carga).

## <a name="secure-service-administration"></a>Administração segura do serviço
O pessoal de operações do Azure é necessário para usar estações de trabalho de administração segura (SAWs). Os clientes podem implementar controles semelhantes usando estações de trabalho com acesso privilegiado. Com o SAWs, a equipe administrativa usa uma conta administrativa individualmente atribuída que é separada da conta de usuário padrão do usuário. O visto se baseia nessa prática de separação de conta, fornecendo uma estação de trabalho confiável para essas contas confidenciais.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
