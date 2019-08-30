---
title: Provisionando logs no portal de Azure Active Directory (versão prévia) | Microsoft Docs
description: Introdução ao provisionamento de relatórios de atividade no portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b2d80d21822758991ad8b3bc1d3bb44e2257f4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129812"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisionando relatórios no portal de Azure Active Directory (versão prévia)

A arquitetura de relatórios no Azure Active Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - Os logs de auditoria dos logs de auditoria fornecem informações de atividade do sistema sobre gerenciamento de usuários e grupos, aplicativos gerenciados e atividades de diretório.[](concept-audit-logs.md)  - 
    - **Logs de provisionamento** – forneça a atividade do sistema sobre usuário, grupos e funções que são provisionadas pelo serviço de provisionamento do Azure AD. 

- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** – um [usuário arriscado](concept-user-at-risk.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral do relatório de provisionamento.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?
* Usuários das funções administrador de segurança, leitor de segurança, leitor de relatórios, administrador de aplicativos e administrador de aplicativos de nuvem
* Administradores globais


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Qual licença do Azure AD você precisa para acessar as atividades de provisionamento?

Seu locatário deve ter uma licença de Azure AD Premium associada a ele para ver o relatório de atividade de provisionamento. Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory. 

## <a name="provisioning-logs"></a>Logs de provisionamento

Os logs de provisionamento fornecem respostas para as seguintes perguntas:

* Quais grupos foram criados com êxito no ServiceNow?
* Como as funções foram importadas do Amazon Web Services?
* Quais usuários não foram criados com êxito no DropBox?

Você pode acessar os logs de provisionamento selecionando **logs de provisionamento** na seção **monitoramento** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registros de provisionamento sejam exibidos no Portal.

![Logs de provisionamento](./media/concept-provisioning-logs/access-provisioning-logs.png "Logs de provisionamento")


Um log de provisionamento tem uma exibição de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema de destino
- O status
- A data


![Colunas padrão](./media/concept-provisioning-logs/default-columns.png "Colunas padrão")

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Seletor de coluna](./media/concept-provisioning-logs/column-chooser.png "Seletor de coluna")

Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

![Colunas disponíveis](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Informações detalhadas](./media/concept-provisioning-logs/detailed-information.png "Informações detalhadas")


## <a name="filter-provisioning-activities"></a>Filtrar atividades de provisionamento

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de provisionamento usando os campos padrão a seguir. Observe que os valores nos filtros são preenchidos dinamicamente com base em seu locatário. Se, por exemplo, você não tiver nenhum evento de criação em seu locatário, não haverá uma opção de filtro para Create.

- identidade
- Action
- Sistema de origem
- Sistema de destino
- State
- Date


![Filtrar](./media/concept-provisioning-logs/filter.png "Filtrar")

O filtro de **identidade** permite que você especifique o nome ou a identidade sobre a qual você se preocupa. Essa identidade pode ser um usuário, grupo, função ou outro objeto. Você pode Pesquisar pelo nome ou ID do objeto. A ID varia de acordo com o cenário. Por exemplo, ao provisionar um objeto do Azure AD para o SalesForce, a ID de origem é a ID de objeto do usuário no Azure AD, enquanto a TargetId é a ID do usuário no Salesforce. Ao provisionar do WORKDAY para Active Directory, a ID de origem é a ID de funcionário do workday Worker. Observe que o nome do usuário talvez nem sempre esteja presente na coluna de identidade. Sempre haverá uma ID. 

O filtro do **sistema de origem** permite que você especifique onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para o ServiceNow, o sistema de origem é o Azure AD. 

O filtro do **sistema de destino** permite especificar para onde a identidade está sendo provisionada. Por exemplo, ao provisionar um objeto do Azure AD para o ServiceNow, o sistema de destino é ServiceNow. 

O filtro de **status** permite que você selecione:

- Todos
- Êxito
- Falha
- Ignorada

O filtro de **ação** permite filtrar o:

- Criar 
- Atualizar
- Eliminar
- Desativar
- Outros

O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de Tempo Personalizado

Ao selecionar um período de tempo personalizado, você pode configurar uma data de início e uma data de término.


Além dos campos padrão, quando selecionado, você também pode incluir os seguintes campos em seu filtro:

- **ID do trabalho** – uma ID de trabalho exclusiva está associada a cada aplicativo para o qual você habilitou o provisionamento.   

- **ID do ciclo** -identifica exclusivamente o ciclo de provisionamento. Você pode compartilhar essa ID para dar suporte à pesquisa do ciclo em que esse evento ocorreu.

- **ID de alteração** -identificador exclusivo do evento de provisionamento. Você pode compartilhar essa ID para dar suporte à pesquisa do evento de provisionamento.   



  

## <a name="provisioning-details"></a>Detalhes de provisionamento 

Ao selecionar um item no modo de exibição de lista de provisionamento, você obtém mais detalhes sobre esse item.
Os detalhes são agrupados com base nas seguintes categorias:

- Passos

- Solução de problemas e recomendações

- Propriedades modificadas

- Resumo


![Filtrar](./media/concept-provisioning-logs/provisioning-tabs.png "Guias")



### <a name="steps"></a>Passos

A guia **etapas** descreve as etapas executadas para provisionar um objeto. O provisionamento de um objeto pode consistir em quatro etapas: 

- Importar objeto
- Determinar se o objeto está no escopo
- Corresponder objeto entre origem e destino
- Provisionar objeto (executar ação-pode ser criar, atualizar, excluir ou desabilitar)



![Filtrar](./media/concept-provisioning-logs/steps.png "Filtrar")


### <a name="troubleshoot-and-recommendations"></a>Solução de problemas e recomendações


A guia **solucionar problemas e recomendações** fornece o código de erro e o motivo. As informações de erro só estão disponíveis no caso de uma falha. 


### <a name="modified-properties"></a>Propriedades modificadas

As **propriedades modificadas** mostram o valor antigo e o novo valor. Nos casos em que não há nenhum valor antigo, a coluna valor antigo fica em branco. 


### <a name="summary"></a>Resumo

A guia **Resumo** fornece uma visão geral do que aconteceu e identificadores para o objeto no sistema de origem e de destino. 

## <a name="what-you-should-know"></a>O que deve saber

- O portal do Azure repositórios relataram dados de provisionamento por 30 dias se você tiver uma edição Premium e 7 dias se tiver uma edição gratuita.

- Você pode usar o atributo ID de alteração como um identificador exclusivo. Isso é, por exemplo, útil ao interagir com o suporte ao produto.

- Atualmente, não há nenhuma opção para baixar dados de provisionamento.

- No momento, não há suporte para o log Analytics.

- Quando você acessa os logs de provisionamento do contexto de um aplicativo, ele não filtra automaticamente os eventos para o aplicativo específico, como faz os logs de auditoria.

## <a name="next-steps"></a>Passos Seguintes

* [Verificar o status do provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problema ao configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


