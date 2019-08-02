---
title: Integridade da infraestrutura do Azure
description: Este artigo aborda a integridade da infraestrutura do Azure.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: ef81e74b07a351139aa8feefbdf1b89ea7e4994f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727194"
---
# <a name="azure-infrastructure-integrity"></a>Integridade da infraestrutura do Azure

## <a name="software-installation"></a>Instalação de software
Todos os componentes na pilha de software instalados no ambiente do Azure são personalizados criados seguindo o processo SDL (Microsoft Security Development Lifecycle). Todos os componentes de software, incluindo imagens do sistema operacional (SO) e banco de dados SQL, são implantados como parte do processo de gerenciamento de alterações e de liberações. O sistema operacional que é executado em todos os nós é uma versão personalizada do Windows Server 2008 ou do Windows Server 2012. A versão exata é escolhida pelo controlador de malha (FC) de acordo com a função que ela pretende que o sistema operacional reproduza. Além disso, o sistema operacional do host não permite a instalação de nenhum componente de software não autorizado.

Alguns componentes do Azure são implantados como clientes do Azure em uma VM convidada em execução em um sistema operacional convidado.

## <a name="virus-scans-on-builds"></a>Verificações de vírus em builds
As compilações do componente de software do Azure (incluindo o SO) precisam passar por uma verificação de vírus que usa a ferramenta de antivírus Endpoint Protection. Cada verificação de vírus cria um log dentro do diretório de compilação associado, detalhando o que foi verificado e os resultados da verificação. A verificação de vírus faz parte do código-fonte da compilação para cada componente no Azure. O código não é movido para a produção sem ter uma verificação de vírus limpa e bem-sucedida. Se algum problema for observado, o Build será congelado e, em seguida, vai para as equipes de segurança do Microsoft Security para identificar onde o código "Rogue" entrou na compilação.

## <a name="closed-and-locked-environment"></a>Ambiente fechado e bloqueado
Por padrão, os nós de infraestrutura do Azure e as VMs convidadas não têm contas de usuário criadas neles. Além disso, as contas padrão de administrador do Windows também são desabilitadas. Os administradores do suporte ao vivo do Azure podem, com a autenticação adequada, fazer logon nesses computadores e administrar a rede de produção do Azure para reparos de emergência.

## <a name="azure-sql-database-authentication"></a>Autenticação do banco de dados SQL do Azure
Assim como ocorre com qualquer implementação de SQL Server, o gerenciamento de conta de usuário deve ser rigidamente controlado. O banco de dados SQL do Azure dá suporte apenas à autenticação SQL Server. Para complementar o modelo de segurança de dados de um cliente, as contas de usuário com senhas fortes e configuradas com direitos específicos também devem ser usadas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACLs e firewalls entre a rede corporativa da Microsoft e um cluster do Azure
As listas de controle de acesso (ACLs) e os firewalls entre a plataforma de serviço e a rede corporativa da Microsoft protegem instâncias do banco de dados SQL contra acesso não autorizado do insider. Além disso, somente os usuários de intervalos de endereços IP da rede corporativa da Microsoft podem acessar o ponto de extremidade de gerenciamento de plataforma Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACLs e firewalls entre nós em um cluster de banco de dados SQL
Como uma proteção adicional, como parte da estratégia de defesa profunda, as ACLs e um firewall foram implementados entre os nós em um cluster de banco de dados SQL. Toda a comunicação dentro do cluster Windows Fabric plataforma, bem como todos os códigos em execução é confiável.

## <a name="custom-monitoring-agents"></a>Agentes de monitoramento personalizados
O banco de dados SQL emprega agentes de monitoramento personalizados (MAs), também chamados de Watchdogs, para monitorar a integridade do cluster do banco de dados SQL.

## <a name="web-protocols"></a>Protocolos da Web

### <a name="role-instance-monitoring-and-restart"></a>Monitoramento e reinicialização da instância de função
O Azure garante que todas as funções implantadas, em execução (funções da Internet ou de trabalho de processamento de back-end) estejam sujeitas a monitoramento de integridade sustentado para garantir que elas forneçam de forma eficiente e eficiente os serviços para os quais foram provisionados. Se uma função se tornar não íntegra, por uma falha crítica no aplicativo que está sendo hospedado ou por um problema de configuração subjacente dentro da própria instância de função, o FC detectará o problema dentro da instância de função e iniciará um estado corretivo.

### <a name="compute-connectivity"></a>Conectividade de computação
O Azure garante que o aplicativo ou serviço implantado seja acessível por meio de protocolos baseados na Web padrão. As instâncias virtuais de funções da Web voltadas para a Internet têm conectividade com a Internet externa e podem ser acessadas diretamente por usuários da Web. Para proteger a sensibilidade e a integridade das operações que as funções de trabalho executam em nome das instâncias virtuais de função Web acessíveis publicamente, as instâncias virtuais de funções de trabalho de processamento back-end têm conectividade com a Internet externa, mas não podem ser acessado diretamente por usuários da Web externos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
