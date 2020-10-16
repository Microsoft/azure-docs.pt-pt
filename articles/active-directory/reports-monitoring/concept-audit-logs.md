---
title: Relatórios de atividade de auditoria no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividade de auditoria no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 994fe6c5005eeeab1399091dca5f72024fdd7262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812595"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatório de atividade de auditoria no portal do Azure Active Directory 

Com relatórios do Azure Ative Directory (Azure AD), pode obter a informação necessária para determinar como está o seu ambiente.



A arquitetura de reporte consiste nos seguintes componentes:

- **Atividade** 
    - **Ins-** O [relatório de inscrições](concept-sign-ins.md) fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.
    - **Registos de auditoria** - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
    - Registos de **provisionamento**  -  [O fornecimento de registos](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) permite que os clientes monitorizem a atividade através do serviço de fornecimento, como a criação de um grupo no ServiceNow ou um utilizador importado do Workday. 
- **Segurança** 
    - **Entradas arriscadas** - Um [sinal de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador. 
    - **Utilizadores sinalizados para o risco** - Um [utilizador de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este artigo dá-lhe uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Utilizadores no **Administrador de Segurança**, Leitor **de Segurança,** **Leitor de Relatórios,** **Leitor Global** ou **Administrador Global**

## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria da Azure AD fornecem registos de atividades do sistema para o cumprimento. Para aceder ao relatório de auditoria, selecione **os registos** de auditoria na secção de **Monitorização** do **Diretório Ativo Azure**. 



Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o serviço que registou a ocorrência
- a categoria e o nome da atividade *(o que)* 
- o estado da atividade (sucesso ou insucesso)
- o destino
- o iniciador / ator (quem) de uma atividade

![Registos de auditoria](./media/concept-audit-logs/listview.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Colunas de auditoria](./media/concept-audit-logs/columns.png "Colunas de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Remover campos](./media/concept-audit-logs/columnselect.png "Remover campos")

Selecione um item na vista da lista para obter informações mais detalhadas.

![selecionar item](./media/concept-audit-logs/details.png "Selecione item")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Pode filtrar os dados da auditoria nos seguintes campos:

- Serviço
- Categoria
- Atividade
- Estado
- Destino
- Iniciado por (Actor)
- Intervalo de datas

![Objeto de filtro](./media/concept-audit-logs/filter.png "Objeto de filtro")

O filtro **'Serviço'** permite-lhe selecionar a partir de uma lista de drop-down dos seguintes serviços:

- Todos
- AAD Management UX
- Revisões de Acesso
- Aprovisionamento de Contas
- Proxy da aplicação
- Métodos de Autenticação
- B2C
- Acesso Condicional
- Diretório do Núcleo
- Gestão de Direitos
- Autenticação híbrida
- Identity Protection
- Utilizadores Convidados
- Serviço MIM
- MyApps
- PIM
- Gestão de Grupos Personalizada
- Gestão de Palavra-passe Personalizada
- Termos de Utilização

O filtro **Category** permite-lhe selecionar um dos seguintes filtros:

- Todos
- AdministrativeUnit
- Gestão de Aplicações
- Autenticação
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- Diretório
- Gestão de Direitos
- Gestão de Grupos
- KerberosDomain
- Gestão de Chaves
- Etiqueta
- Outro
- PermissõesGrantPolicy
- Política
- Gestão de Recursos
- Gestão de Papéis
- Gestão de Utilizador

O filtro **De Atividade** baseia-se na categoria e na seleção do tipo de recurso de atividade que faz. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

Pode obter a lista de todas as Atividades de Auditoria utilizando a API do gráfico: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

O filtro **Status** permite filtrar com base no estado de uma operação de auditoria. O estatuto pode ser um dos seguintes:

- Todos
- Success
- Falha

O filtro **Target** permite-lhe procurar um alvo específico através do início do nome ou nome principal do utilizador (UPN). O nome-alvo e a UPN são sensíveis a casos. 

O **Iniciado por** filtro permite-lhe definir o nome de um ator ou um nome principal universal (UPN). O nome e a UPN são sensíveis a casos.

O filtro **de gama Data** permite definir um prazo para os dados devolvidos.  
Os valores possíveis são:

- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Também pode optar por descarregar os dados filtrados, até 250.000 registos, selecionando o botão **Descarregar.** Pode descarregar os registos no formato CSV ou JSON. O número de registos que pode descarregar está limitado pelas políticas de retenção do [relatório Azure Ative Directory](reference-reports-data-retention.md).

![Transferir dados](./media/concept-audit-logs/download.png "Transferir dados")

## <a name="audit-logs-shortcuts"></a>Atalhos dos registos de auditoria

Para além do **Azure Active Directory**, o portal do Azure proporciona-lhe dois pontos de entrada adicionais para dados de auditoria:

- Utilizadores e grupos
- Aplicações Empresariais

### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações foram aplicadas aos utilizadores?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se pretender rever apenas os dados de auditoria que estão relacionados com os utilizadores, pode encontrar uma visão filtrada nos **registos** de Auditoria na secção de **Monitorização** do **separador Utilizadores.** Este ponto de entrada tem **a Gestão do Utilizador** como categoria pré-selecionado.

![Utilizador](./media/concept-audit-logs/users.png "Utilizador")

Se pretender rever apenas os dados de auditoria que estão relacionados com grupos, pode encontrar uma visão filtrada nos **registos** de auditoria na secção de **Monitorização** do separador **Grupos.** Este ponto de entrada tem **a GroupManagement** como categoria pré-selecionado.

![Grupos de filtros](./media/concept-audit-logs/groups.png "Grupos de filtros")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Que aplicações foram adicionadas ou atualizadas?
* Que pedidos foram removidos?
* O diretor de serviço para uma aplicação foi alterado?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se pretender rever os dados de auditoria relacionados com as suas aplicações, pode encontrar uma vista filtrada nos **registos de Auditoria** na secção **Atividade** da lâmina de aplicações da **Enterprise.** Este ponto de entrada tem **aplicações da Empresa** pré-selecionadas como **o Tipo de Aplicação**.

![Aplicações empresariais](./media/concept-audit-logs/enterpriseapplications.png "Aplicações Empresariais")

## <a name="microsoft-365-activity-logs"></a>Registos de atividades da Microsoft 365

Pode ver os registos de atividade do Microsoft 365 a partir do [centro de administração Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Apesar de a atividade da Microsoft 365 e os registos de atividade azure aduários partilharem muitos dos recursos do diretório, apenas o centro de administração Microsoft 365 fornece uma visão completa dos registos de atividade da Microsoft 365. 

Também pode aceder aos registos de atividades do Microsoft 365 programáticamente utilizando as [APIs de Gestão do Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos seguintes

- [Referência da atividade de auditoria do Azure AD](reference-audit-activities.md)
- [Azure AD reporta referência de retenção](reference-reports-data-retention.md)
- [Referência de atrasos de log Azure AD](reference-reports-latencies.md)
