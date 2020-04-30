---
title: Padrão de aplicação web multi-inquilinos / Microsoft Docs
description: Encontre visões arquitetônicas e padrões de design que descrevem como implementar uma aplicação web multi-inquilino no Azure.
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
ms.openlocfilehash: d1441ede9f448b3e6ffb0726c2ee92f192369e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481841"
---
# <a name="multitenant-applications-in-azure"></a>Aplicações Multi-inquilino no Azure
Uma aplicação multiarrendatária é um recurso partilhado que permite que "utilizadores em inquilinos separados" vejam a aplicação como se fosse sua. Um cenário típico que se presta a uma aplicação multiarrendatária é aquele em que todos os utilizadores da aplicação de diferentes inquilinos podem querer personalizar a experiência do utilizador, mas de outra forma têm os mesmos requisitos básicos de negócio. Exemplos de grandes aplicações multiarrendatárias são o Office 365, Outlook.com e visualstudio.com.

Do ponto de vista de um fornecedor de aplicações, os benefícios do multiarrendamento dizem principalmente respeito à eficiência operacional e aos custos. Uma versão da sua aplicação pode atender às necessidades de muitos inquilinos/clientes, permitindo a consolidação de tarefas de administração do sistema, tais como monitorização, afinação de desempenho, manutenção de software e backups de dados.

O seguinte fornece uma lista dos objetivos e requisitos mais significativos do ponto de vista do fornecedor.

* **Provisionamento**: Você deve ser capaz de fornecer novos inquilinos para o pedido.  Para aplicações multiarrendatárias com um grande número de inquilinos, é geralmente necessário automatizar este processo permitindo o fornecimento de self-service.
* **Manutenção**: Deve ser capaz de atualizar a aplicação e executar outras tarefas de manutenção enquanto vários inquilinos a utilizam.
* **Monitorização**: Deve poder monitorizar sempre a aplicação para identificar quaisquer problemas e resolver os problemas. Isto inclui monitorizar a forma como cada inquilino está a usar a aplicação.

Uma aplicação multiarrendatária devidamente implementada proporciona os seguintes benefícios aos utilizadores.

* **Isolamento**: As atividades dos inquilinos individuais não afetam a utilização do pedido por outros inquilinos. Os inquilinos não podem aceder aos dados uns dos outros. Parece ao inquilino como se tivesse uso exclusivo da aplicação.
* **Disponibilidade**: Os inquilinos individuais querem que a aplicação esteja constantemente disponível, talvez com garantias definidas num SLA. Mais uma vez, as atividades de outros inquilinos não devem afetar a disponibilidade do pedido.
* **Escalabilidade**: As escalas de candidatura para satisfazer a procura de inquilinos individuais. A presença e as ações de outros inquilinos não devem afetar o desempenho do pedido.
* **Custos**: Os custos são mais baixos do que executar uma aplicação dedicada, de um único inquilino, porque o multiarrendamento permite a partilha de recursos.
* **Customizabilidade.** A capacidade de personalizar a aplicação para um inquilino individual de várias formas, como adicionar ou remover funcionalidades, mudar de cores e logótipos, ou até mesmo adicionar o seu próprio código ou script.

Em suma, embora existam muitas considerações que deve ter em conta para prestar um serviço altamente escalável, existem também uma série de objetivos e requisitos que são comuns a muitas aplicações multiarrendatárias. Alguns podem não ser relevantes em cenários específicos, e a importância dos objetivos e requisitos individuais será diferente em cada cenário. Como fornecedor da aplicação multiarrendatária, terá também objetivos e requisitos como, de acordo com os objetivos e requisitos do arrendatário, rentabilidade, faturação, vários níveis de serviço, provisionamento, monitorização da manutenção e automação.

Para obter mais informações sobre considerações adicionais sobre o design de uma aplicação multiarrendatária, consulte a apresentação de [uma aplicação multi-arrendatária no Azure][Hosting a Multi-Tenant Application on Azure]. Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

O Azure fornece muitas funcionalidades que lhe permitem resolver os principais problemas encontrados ao conceber um sistema multiarrendatário.

**Isolamento**

* Site de segmento Inquilinos por Cabeçalhos anfitriãos com ou sem comunicação TLS
* Site de segmento inquilinos por parâmetros de consulta
* Serviços Web em Funções de Trabalhador
  * Funções de Trabalhador que normalmente processam dados sobre o backend de uma aplicação.
  * Funções Web que normalmente funcionam como o frontend para aplicações.

**Armazenamento**

Gestão de dados como a Base de Dados Azure SQL ou os serviços de Armazenamento Azure, como o serviço Table, que presta serviços de armazenamento de grandes quantidades de dados não estruturados e o serviço Blob, que presta serviços para armazenar grandes quantidades de texto não estruturado ou dados binários, como vídeo, áudio e imagens.

* Assegurar dados multiarrendatários em bases de dados SQL por inquilino SQL Server logins.
* Utilizando tabelas Azure para recursos de aplicação, especificando uma política de acesso ao nível do contentor, pode ter a capacidade de ajustar permissões sem ter de emitir novos URL's para os recursos protegidos com assinaturas de acesso partilhado.
* As filas azure para recursos de aplicação As filas Azure são comumente usadas para impulsionar o processamento em nome dos inquilinos, mas também podem ser usadas para distribuir trabalhos necessários para o provisionamento ou gestão.
* Filas de ônibus de serviço para recursos de aplicação que empurram o trabalho para um serviço partilhado, você pode usar uma única fila onde cada remetente de inquilino somente tem permissões (como derivado de reclamações emitidas da ACS) para empurrar para essa fila, enquanto apenas os recetores do serviço têm permissão para retirar da fila os dados provenientes de vários inquilinos.

**Serviços de Ligação e Segurança**

* Azure Service Bus, uma infraestrutura de mensagens que se situa entre aplicações que lhes permite trocar mensagens de forma vagamente acoplada para uma melhor escala e resiliência.

**Serviços de Networking**

O Azure disponibiliza vários serviços de networking que suportam a autenticação e melhoram a gestão das suas aplicações hospedadas. Estes serviços incluem o seguinte:

* A Rede Virtual Azure permite-lhe fornecer e gerir redes privadas virtuais (VPNs) em Azure, bem como ligá-las de forma segura à infraestrutura de TI no local.
* O Virtual Network Traffic Manager permite-lhe carregar o tráfego de tráfego de saldo através de vários serviços azure hospedados, quer estejam a funcionar no mesmo datacenter ou em diferentes datacenters em todo o mundo.
* O Azure Ative Directory (Azure AD) é um serviço moderno e baseado em REST que fornece capacidades de gestão de identidade e controlo de acesso para as suas aplicações na nuvem. A utilização de AD Azure para Recursos de Aplicação proporciona uma forma fácil de autenticar e autorizar os utilizadores a terem acesso às suas aplicações e serviços web, permitindo ao mesmo tempo que as funcionalidades de autenticação e autorização sejam tidas em conta do seu código.
* A Azure Service Bus fornece uma capacidade segura de mensagens e fluxo de dados para aplicações distribuídas e híbridas, tais como comunicação entre aplicações e aplicações e serviços no local, sem necessitar de infraestruturas e de segurança complexas. A utilização de Recursos de Autocarrode Serviços para Recursos de Aplicação para aceder aos serviços expostos como pontos finais pode pertencer ao inquilino (por exemplo, hospedado fora do sistema, como no local), ou podem ser serviços prestados especificamente para o inquilino (porque os dados sensíveis e específicos do arrendatário viajam através deles).

**Recursos de Provisionamento**

A Azure fornece uma série de formas de fornecer novos inquilinos para a candidatura. Para aplicações multiarrendatárias com um grande número de inquilinos, é geralmente necessário automatizar este processo permitindo o fornecimento de self-service.

* As funções dos trabalhadores permitem-lhe fornecer e desprovisionar recursos de inquilinos (como quando um novo inquilino se inscreve ou cancela), recolher métricas para uso de medição e gerir a escala seguindo um determinado horário ou em resposta à passagem de limiares de indicadores-chave de desempenho. Esta mesma função também pode ser usada para empurrar atualizações e atualizações para a solução.
* As Bolhas Azure podem ser usadas para fornecer recursos de armazenamento de computação ou pré-inicializados para novos inquilinos, ao mesmo tempo que fornece políticas de acesso ao nível dos contentores para proteger o serviço de cálculo Pacotes, imagens VHD e outros recursos.
* As opções de fornecimento de recursos de base de dados SQL para um inquilino incluem:
  
  * DDL em scripts ou incorporados como recursos dentro de conjuntos.
  * Pacotes SQL Server 2008 R2 DAC implantados programáticamente.
  * Cópia de uma base de dados de referência principal.
  * Utilização da base de dados Import and Export para fornecer novas bases de dados a partir de um ficheiro.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
