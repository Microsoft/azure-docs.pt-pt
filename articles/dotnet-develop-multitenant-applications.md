---
title: Padrão de aplicação Web do multi-inquilino | Documentos da Microsoft
description: Encontre visões gerais de arquitetura e padrões de design que descrevem como implementar uma aplicação web do multi-inquilino no Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: 92a0caedca34756228dbf57ec9099fd2ece3d84e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225976"
---
# <a name="multitenant-applications-in-azure"></a>Aplicações multi-inquilino no Azure
Uma aplicação multi-inquilino é um recurso compartilhado, que permite que os utilizadores separados, ou "inquilinos" ver a aplicação que foi seus próprios. Um cenário típico que presta-se a uma aplicação multi-inquilino é aquele em que todos os utilizadores da aplicação podem pretender personalizar a experiência do usuário, mas têm os mesmos requisitos de negócios básico. Exemplos de aplicações multi-inquilino grandes são do Office 365, Outlook.com e visualstudio.com.

Da perspectiva de um fornecedor de aplicações, os benefícios da arquitetura "multitenancy" principalmente se relacionam com a eficiência operacional e de custo. Uma versão do seu aplicativo pode atender às necessidades de muitos inquilinos/clientes, que permite a consolidação do sistema de tarefas de administração como monitorização, ajuste de desempenho, manutenção de software e as cópias de segurança de dados.

O seguinte fornece uma lista dos requisitos do ponto de vista de um fornecedor e objetivos mais importantes.

* **Aprovisionamento**: Tem de ser capaz de aprovisionar novos inquilinos para a aplicação.  Para aplicações multi-inquilino com um grande número de inquilinos, é normalmente necessário automatizar este processo, permitindo que o provisionamento de autoatendimento.
* **Facilidade de manutenção**: Tem de ser capaz de atualizar a aplicação e efetuar outras tarefas de manutenção, enquanto vários inquilinos estão a utilizar.
* **Monitorização**: Tem de ser capaz de monitorizar a aplicação em todos os momentos para identificar quaisquer problemas e para resolver o problema. Isto inclui como cada inquilino está a utilizar a aplicação de monitorização.

Uma aplicação multi-inquilino corretamente implementada proporciona as seguintes vantagens para os utilizadores.

* **Isolamento**: As atividades de inquilinos individuais não afetam a utilização da aplicação por outros inquilinos. Os inquilinos não é possível aceder a dados uns dos outros. Parece-se ao inquilino como se eles têm a utilização exclusiva do aplicativo.
* **Disponibilidade**: Os inquilinos individuais querem que o aplicativo esteja sempre disponível, talvez com garantias definidas num SLA. Novamente, as atividades de outros inquilinos não devem afetar a disponibilidade da aplicação.
* **Escalabilidade**: O aplicativo é dimensionado para satisfazer a procura de inquilinos individuais. A presença e ações de outros inquilinos não devem afetar o desempenho do aplicativo.
* **Os custos**: Os custos são mais baixos do que a execução de um aplicativo de inquilino único dedicado porque vários inquilinos permitem a partilha de recursos.
* **Capacidade de personalização**. A capacidade de personalizar a aplicação para um inquilino individual de várias formas, como adição ou remoção de recursos, alterar cores e logotipos ou até mesmo adicionar seu próprio código ou script.

Em suma, embora haja várias considerações que deve levar em conta para fornecer um serviço altamente dimensionável, há também um número de objetivos e requisitos que são comuns a muitas aplicações multi-inquilino. Algumas podem não ser relevantes em cenários específicos e a importância dos requisitos e metas individuais serão diferentes em cada cenário. Como um fornecedor da aplicação multi-inquilino, também terá objetivos e requisitos, tais como de reunião do inquilino objetivos e requisitos, rentabilidade, faturação, vários níveis de serviço, aprovisionamento, a monitorização de capacidade de manutenção e automação.

Para obter mais informações sobre considerações de design adicionais de uma aplicação multi-inquilino, consulte [que aloja uma aplicação multi-inquilino no Azure][Hosting a Multi-Tenant Application on Azure]. Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

O Azure fornece muitos recursos que permitem-lhe resolver os problemas principais encontrados quando um sistema multi-inquilino.

**Isolamento**

* Inquilinos de Web site de segmento por cabeçalhos de anfitrião com ou sem a comunicação SSL
* Inquilinos de Web site de segmento por parâmetros de consulta
* Web Services em funções de trabalho
  * Funções de trabalho que normalmente processam dados no back-end de um aplicativo.
  * Funções da Web que normalmente atuam como o front-end para aplicações.

**Armazenamento**

Gestão de dados, como os serviços de base de dados do Azure SQL ou o armazenamento do Azure, como o serviço de tabela, que fornece serviços para o armazenamento de grandes quantidades de dados não estruturados e o serviço de BLOBs, que fornece serviços para armazenar grandes quantidades de texto não estruturado ou obter dados binários como vídeo, áudio e imagens.

* Proteger dados de multi-inquilino na base de dados SQL inícios de sessão do SQL Server por inquilino.
* Utilizar tabelas do Azure para recursos de aplicativos ao especificar uma política de acesso de nível de contêiner, pode ter a capacidade de ajustar as permissões sem a necessidade de emitir o novo URL para os recursos protegidos com assinaturas de acesso partilhado.
* As filas do Azure para as filas do Azure de recursos de aplicação são frequentemente utilizadas para processamento de unidade em nome de inquilinos, mas também podem ser utilizadas para distribuir o trabalho necessário para o aprovisionamento ou gestão.
* Filas do Service Bus para recursos de aplicação que envia por push trabalhar um partilhado para um serviço, pode utilizar uma fila única onde cada remetente de inquilino apenas tiver permissões (como derivado de afirmações emitidas do ACS) para enviar por push para essa fila, embora tenham apenas os recetores do serviço permissão de fila de onde os dados provenientes de vários inquilinos.

**Serviços de segurança e de ligação**

* O Azure Service Bus, uma infraestrutura de mensagens que fica entre aplicações que lhes permite trocar mensagens de uma maneira flexível para uma melhor escala e resiliência.

**Serviços de redes**

O Azure fornece vários serviços de rede que suportam a autenticação e melhorar a capacidade de gerenciamento de seus aplicativos hospedados. Esses serviços incluem o seguinte:

* Permite a rede Virtual do Azure, Aprovisiona e gere redes privadas virtuais (VPNs) no Azure, bem como em segurança a ligá-las com infraestrutura de TI no local.
* Gestor de tráfego de rede virtual permite-lhe carga balancear o tráfego de entrada em vários serviços alojados do Azure, independentemente de estarem a ser executados no mesmo datacenter ou em diferentes datacenters em todo o mundo.
* Azure Active Directory (Azure AD) é um serviço de moderno e baseados em REST que fornece capacidades de controlo de acesso e gestão de identidades para as suas aplicações na cloud. Utilizar o Azure AD para os recursos de aplicação fornece uma forma fácil de autenticar e autorizar utilizadores para obter acesso aos seus serviços e aplicações web, permitindo que os recursos de autenticação e autorização para ser fatorados de seu código.
* O Azure Service Bus fornece um sistema de mensagens seguro e distribuído da funcionalidade de fluxo de dados para e aplicações híbridas, como a comunicação entre o Azure aplicações e aplicações no local e serviços alojados, sem a necessidade de segurança e complexas de firewall infraestruturas. Utilizar o reencaminhamento do Service Bus para recursos de aplicativos para acessar os serviços que são expostos como pontos finais poderá pertencer ao inquilino (por exemplo, hospedado fora do sistema, tais como no local) ou podem ser aprovisionados especificamente para o inquilino porque (os serviços dados confidenciais, específico de inquilino circulam por elas).

**O aprovisionamento de recursos**

O Azure fornece várias formas para aprovisionar novos inquilinos para a aplicação. Para aplicações multi-inquilino com um grande número de inquilinos, é normalmente necessário automatizar este processo, permitindo que o provisionamento de autoatendimento.

* Funções de trabalho permitem-lhe aprovisionar e aprovisionar eliminação por inquilino recursos (por exemplo, quando um novo inquilino sinais de segurança ou cancela), recolher métricas de medição de utilizar e gerir o dimensionamento seguir uma programação determinada ou em resposta a cruzamento de limites de chave de desempenho indicadores. Esta mesma função também pode ser utilizada para disponibilizar atualizações e melhoramentos para a solução.
* Blobs do Azure podem ser utilizados para aprovisionar computação ou previamente inicializados pacotes, imagens VHD e outros recursos de serviço de recursos de armazenamento para novos inquilinos ao mesmo tempo, as políticas de acesso de nível de contêiner proteger a computação.
* As opções para o aprovisionamento de recursos de base de dados SQL para um inquilino incluem:
  
  * DDL em scripts ou incorporados como recursos em assemblies.
  * SQL Server 2008 R2 pacotes DAC implementado por meio de programação.
  * Copiar de uma base de dados de referência principal.
  * Utilizar base de dados, importar e exportar para aprovisionar novas bases de dados de um ficheiro.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
