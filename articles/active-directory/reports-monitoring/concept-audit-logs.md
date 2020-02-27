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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648430"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatório de atividade de auditoria no portal do Azure Active Directory 

Com os relatórios azure Ative Directory (Azure AD), pode obter a informação necessária para determinar como está o seu ambiente.

A arquitetura de reporte consiste nos seguintes componentes:

- **Atividade** 
    - **Iniciar** sessão – O [relatório de inscrição](concept-sign-ins.md) fornece informações sobre a utilização de aplicações geridas e atividades de inscrição no utilizador.
    - **Registos de auditoria** - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do Azure AD, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- **Segurança** 
    - **Inscrições arriscadas** - Um [sinal de inscrição arriscado](concept-risky-sign-ins.md) é um indicador para uma tentativa de inscrição que poderia ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador. 
    - **Utilizadores sinalizados para o risco** - Um [utilizador arriscado](concept-user-at-risk.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este artigo dá-lhe uma visão geral do relatório de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

* Utilizadores nas funções de Administrador de **Segurança,** **Leitor de Segurança,** **Leitor de Relatórios,** **Leitor Global** ou **Administrador Global**

## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria da AD Azure fornecem registos de atividades do sistema para o cumprimento. Para aceder ao relatório de auditoria, selecione **registos de auditoria** na secção **de Monitorização** do **Diretório Ativo Azure**. Note que os registos de auditoria podem ter uma latência de até uma hora, pelo que pode demorar esse tempo para que os dados da atividade de auditoria apareçam no portal depois de ter concluído a tarefa.



Um registo de auditoria tem uma vista de lista predefinida que mostra:

- a data e hora da ocorrência
- o serviço que registou a ocorrência
- a categoria e o nome da atividade (*o que*) 
- o estado da atividade (sucesso ou insucesso)
- o destino
- o iniciador /ator (que) de uma atividade

![Registos de auditoria](./media/concept-audit-logs/listview.png "Registos de auditoria")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Registos de auditoria](./media/concept-audit-logs/columns.png "Registos de auditoria")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Registos de auditoria](./media/concept-audit-logs/columnselect.png "Registos de auditoria")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Registos de auditoria](./media/concept-audit-logs/details.png "Registos de auditoria")


## <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Pode filtrar os dados de auditoria nos seguintes campos:

- Serviço
- Categoria
- Atividade
- Estado
- Destino
- Iniciado por (Actor)
- Intervalo de datas

![Registos de auditoria](./media/concept-audit-logs/filter.png "Registos de auditoria")

O filtro **Serviço** permite-lhe selecionar a partir de uma lista de drop-down dos seguintes serviços:

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
- Autenticação Híbrida
- Identity Protection
- Utilizadores Convidados
- Serviço MIM
- MyApps
- PIM
- Gestão de Grupos Personalizada
- Gestão de Palavra-passe Personalizada
- Termos de Utilização

O filtro **categoria** permite selecionar um dos seguintes filtros:

- Todos
- AdministrativeUnit
- Gestão de Aplicações
- Autenticação
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomínio
- Gestão de Chaves
- Etiqueta
- Outros
- Política permissão
- Política
- ResourceManagement
- RoleManagement
- Gestão de Utilizadores

O filtro **De Atividade** baseia-se na categoria e na seleção do tipo de recursos de atividade que faz. Pode selecionar uma atividade específica que queira ver ou selecionar todas. 

Pode obter a lista de todas as Atividades de Auditoria utilizando o Gráfico API: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

O filtro **Status** permite filtrar com base no estado de uma operação de auditoria. O estatuto pode ser um dos seguintes:

- Todos
- Êxito
- Falha

O filtro **Target** permite-lhe procurar um determinado alvo com o início do nome principal do utilizador (UPN). O nome-alvo e a UPN são sensíveis aos casos. 

O **Iniciado por** filtro permite-lhe definir o nome de um ator ou um nome principal universal (UPN) com. O nome e upn são sensíveis a casos.

O filtro **de gama Data** permite definir um prazo para os dados devolvidos.  
Os valores possíveis são:

- 7 dias
- 24 horas
- Personalizado

Quando selecionar um período de tempo personalizado, pode configurar uma hora de início e uma hora de fim.

Também pode optar por descarregar os dados filtrados, até 250.000 registos, selecionando o botão **Download.** Pode descarregar os registos em formato CSV ou JSON. O número de registos que pode descarregar está limitado pelas políticas de retenção de [relatórios do Azure Ative Directory.](reference-reports-data-retention.md)

![Registos de auditoria](./media/concept-audit-logs/download.png "Registos de auditoria")

## <a name="audit-logs-shortcuts"></a>Atalhos dos registos de auditoria

Para além do **Azure Active Directory**, o portal do Azure proporciona-lhe dois pontos de entrada adicionais para dados de auditoria:

- Utilizadores e grupos
- Aplicações empresariais

### <a name="users-and-groups-audit-logs"></a>Registos de auditoria de utilizadores e grupos

Com os relatórios de auditoria baseados em utilizadores e grupos, poderá obter respostas a perguntas como:

- Que tipos de atualizações os utilizadores aplicaram?

- Quantos utilizadores foram alterados?

- Quantas palavras-passe foram alteradas?

- O que fez um administrador num diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com as alterações na associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou utilizador?

Se pretender rever apenas dados de auditoria relacionados com os utilizadores, pode encontrar uma vista filtrada nos **registos** de Auditoria na secção **de Monitorização** do separador **Utilizadores.** Este ponto de entrada tem **userManagement** como categoria pré-selecionada.

![Registos de auditoria](./media/concept-audit-logs/users.png "Registos de auditoria")

Se pretender rever apenas dados de auditoria relacionados com grupos, pode encontrar uma vista filtrada nos **registos** de Auditoria na secção **de Monitorização** do separador **Grupos.** Este ponto de entrada tem **a GroupManagement** como categoria pré-selecionada.

![Registos de auditoria](./media/concept-audit-logs/groups.png "Registos de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Registos de auditoria de aplicações empresariais

Com os relatórios de auditoria baseados em aplicações, poderá obter respostas a perguntas como:

* Que aplicações foram adicionadas ou atualizadas?
* Que aplicações foram removidas?
* O diretor de serviço para uma aplicação mudou?
* Os nomes das aplicações foram alterados?
* Quem autorizou uma aplicação?

Se pretender rever os dados de auditoria relacionados com as suas aplicações, pode encontrar uma vista filtrada nos **registos** de Auditoria na secção **de aplicações** da **Empresa.** Este ponto de entrada tem **aplicações da Enterprise** pré-selecionadas como o Tipo de **Aplicação**.

![Registos de auditoria](./media/concept-audit-logs/enterpriseapplications.png "Registos de auditoria")

## <a name="office-365-activity-logs"></a>Registos de atividades do Office 365

Pode ver registos de atividade do Office 365 a partir do centro de administração da [Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora a atividade do Office 365 e os registos de atividade da Azure AD partilhem muitos dos recursos de diretório, apenas o centro de administração da Microsoft 365 fornece uma visão completa dos registos de atividade do Office 365. 

Também pode aceder aos registos de atividade do Office 365 programáticamente utilizando as APIs de [Gestão do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Passos seguintes

- [Referência de atividade de auditoria da AD Azure](reference-audit-activities.md)
- [Azure AD reporta referência de retenção](reference-reports-data-retention.md)
- [Referência de latenciências de registo azure ad](reference-reports-latencies.md)
