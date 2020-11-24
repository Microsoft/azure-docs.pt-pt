---
title: Padrão de aplicação web multi-inquilinos / Microsoft Docs
description: Encontre visões arquitetónicas e padrões de design que descrevem como implementar uma aplicação web multi-inquilino em Azure.
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
ms.custom: devx-track-dotnet
ms.openlocfilehash: d36a2804519e5728dd068cc6c06ad005244e8c95
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524101"
---
# <a name="multitenant-applications-in-azure"></a>Aplicações Multi-inquilino no Azure
Uma aplicação multitenante é um recurso partilhado que permite que "utilizadores em inquilinos separados" vejam a aplicação como se fosse sua. Um cenário típico que se presta a uma aplicação multitenante é aquele em que todos os utilizadores da aplicação de diferentes inquilinos podem querer personalizar a experiência do utilizador, mas de outra forma têm os mesmos requisitos básicos de negócio. Exemplos de grandes aplicações multitenantes são o Microsoft 365, Outlook.com e visualstudio.com.

Do ponto de vista de um fornecedor de aplicações, os benefícios da multitenância dizem principalmente respeito à eficiência operacional e de custos. Uma versão da sua aplicação pode atender às necessidades de muitos inquilinos/clientes, permitindo a consolidação de tarefas de administração do sistema como monitorização, afinação de desempenho, manutenção de software e backups de dados.

O seguinte fornece uma lista dos objetivos e requisitos mais significativos do ponto de vista de um fornecedor.

* **Provisioning**: Deve poder providenciar novos inquilinos para o pedido.  Para aplicações multitenantes com um grande número de inquilinos, é geralmente necessário automatizar este processo, permitindo o fornecimento de self-service.
* **Manutenção :** Deve ser capaz de atualizar a aplicação e executar outras tarefas de manutenção enquanto vários inquilinos a utilizam.
* **Monitorização**: Deve ser capaz de monitorizar sempre a aplicação para identificar quaisquer problemas e resolver os problemas. Isto inclui monitorizar a forma como cada inquilino está a usar a aplicação.

Uma aplicação multitenante devidamente implementada proporciona os seguintes benefícios aos utilizadores.

* **Isolamento**: As atividades dos inquilinos individuais não afetam a utilização do pedido por outros inquilinos. Os inquilinos não podem aceder aos dados uns dos outros. Parece ao inquilino como se tivesse uso exclusivo da aplicação.
* **Disponibilidade**: Os inquilinos individuais querem que a candidatura esteja constantemente disponível, talvez com garantias definidas num SLA. Mais uma vez, as atividades de outros inquilinos não devem afetar a disponibilidade do pedido.
* **Escalabilidade**: As escalas de candidatura satisfazem a procura de inquilinos individuais. A presença e ações de outros inquilinos não devem afetar o desempenho do pedido.
* **Custos**: Os custos são inferiores à execução de uma aplicação dedicada e única, porque o multi-arrendamento permite a partilha de recursos.
* **Personalização**. A capacidade de personalizar a aplicação para um inquilino individual de várias maneiras, como adicionar ou remover funcionalidades, alterar cores e logotipos, ou até mesmo adicionar o seu próprio código ou script.

Em suma, embora existam muitas considerações que deve ter em conta para prestar um serviço altamente escalável, existem também uma série de objetivos e requisitos que são comuns a muitas aplicações multitenantes. Alguns podem não ser relevantes em cenários específicos, e a importância de objetivos e requisitos individuais diferirá em cada cenário. Como fornecedor da aplicação multitenante, você também terá objetivos e requisitos tais como, atender os objetivos e requisitos do inquilino, rentabilidade, faturação, múltiplos níveis de serviço, fornecimento, monitorização de manutenção e automação.

Para obter mais informações sobre considerações adicionais de design de uma aplicação multitenant, consulte [hospedar uma aplicação multi-arrendatário no Azure][Hosting a Multi-Tenant Application on Azure]. Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](./azure-sql/database/saas-tenancy-app-design-patterns.md). 

O Azure fornece muitas funcionalidades que lhe permitem resolver os principais problemas encontrados ao conceber um sistema multitenante.

**Isolamento**

* Segmente Website Inquilinos por Cabeçalhos anfitriões com ou sem comunicação TLS
* Segmentação Website Inquilinos por Parâmetros de Consulta
* Serviços Web em Funções de Trabalhador
  * Funções de Trabalhador que normalmente processam dados sobre o backend de uma aplicação.
  * Web Roles que normalmente funcionam como o frontend para aplicações.

**Armazenamento**

Gestão de dados como Azure SQL Database ou serviços de Armazenamento Azure, como o serviço Table, que fornece serviços de armazenamento de grandes quantidades de dados não estruturados e o serviço Blob, que fornece serviços para armazenar grandes quantidades de texto não estruturado ou dados binários, como vídeo, áudio e imagens.

* Segurança de dados multitenantes em bases de dados SQL por inquilino SQL Server.
* Utilizando tabelas Azure para recursos de aplicação especificando uma política de acesso ao nível do contentor, pode ter a capacidade de ajustar permissões sem ter de emitir novos URL's para os recursos protegidos com assinaturas de acesso partilhado.
* As filas Azure Para Recursos de Aplicação As filas Azure são comumente usadas para impulsionar o processamento em nome dos inquilinos, mas também podem ser usadas para distribuir trabalhos necessários para o provisionamento ou gestão.
* As filas de autocarros de serviço para recursos de aplicação que empurram o trabalho para um serviço partilhado, você pode usar uma única fila onde cada remetente inquilino só tem permissões (derivadas de reclamações emitidas da ACS) para empurrar para essa fila, enquanto apenas os recetores do serviço têm permissão para retirar da fila os dados provenientes de vários inquilinos.

**Serviços de Conexão e Segurança**

* Azure Service Bus, uma infraestrutura de mensagens que se situa entre aplicações que lhes permite trocar mensagens de forma vagamente acoptada para uma escala e resiliência melhoradas.

**Serviços de Rede**

O Azure disponibiliza vários serviços de networking que suportam a autenticação e melhoram a gestão das suas aplicações hospedadas. Estes serviços incluem os seguintes:

* A Azure Virtual Network permite-lhe provisões e gestão de redes privadas virtuais (VPNs) em Azure, bem como ligá-las de forma segura com a infraestrutura de TI no local.
* O Virtual Network Traffic Manager permite-lhe carregar o tráfego de entrada de equilíbrio em vários serviços Azure hospedados, quer estejam a funcionar no mesmo datacenter ou em diferentes centros de dados em todo o mundo.
* O Azure Ative Directory (Azure AD) é um serviço moderno e baseado no REST que fornece capacidades de gestão de identidade e controlo de acesso para as suas aplicações em nuvem. A utilização do Azure AD para Recursos de Aplicação proporciona uma forma fácil de autenticar e autorizar os utilizadores a terem acesso às suas aplicações e serviços web, permitindo que as funcionalidades de autenticação e autorização sejam tidas em conta no seu código.
* A Azure Service Bus fornece uma capacidade segura de fluxo de mensagens e dados para aplicações distribuídas e híbridas, como a comunicação entre aplicações e serviços hospedados no Azure, sem necessidade de infraestruturas complexas de firewall e segurança. Utilizando o Service Bus Relay para recursos de aplicação para aceder aos serviços expostos como pontos finais pode pertencer ao inquilino (por exemplo, hospedado fora do sistema, como no local), ou podem ser serviços prestados especificamente para o arrendatário (porque dados sensíveis e específicos do inquilino viajam através deles).

**Recursos de Provisionamento**

A Azure fornece uma série de formas de fornecer novos inquilinos para o pedido. Para aplicações multitenantes com um grande número de inquilinos, é geralmente necessário automatizar este processo, permitindo o fornecimento de self-service.

* As funções dos trabalhadores permitem-lhe provisão e desinscêncio por recursos de arrendatário (como quando um novo inquilino assina ou cancela), recolher métricas para utilização de medição, e gerir a escala seguindo um determinado horário ou em resposta à passagem de limiares de indicadores de desempenho chave. Esta mesma função também pode ser usada para empurrar atualizações e atualizações para a solução.
* As Blobs Azure podem ser usadas para fornecer recursos de armazenamento computacional ou pré-inicializados para novos inquilinos, ao mesmo tempo que fornece políticas de acesso ao nível do contentor para proteger os pacotes de serviços de computação, imagens VHD e outros recursos.
* As opções de a provisionamento dos recursos da Base de Dados SQL para um inquilino incluem:
  
  * DDL em scripts ou incorporado como recursos dentro de conjuntos.
  * Sql Server 2008 R2 DAC Packages implantados programáticamente.
  * A copiar de uma base de dados de referência principal.
  * Utilização da base de dados Import and Export para a disponibilização de novas bases de dados a partir de um ficheiro.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: /previous-versions/msp-n-p/hh534480(v=pandp.10)
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716