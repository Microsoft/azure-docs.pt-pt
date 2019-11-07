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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612806"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Provisionando relatórios no portal de Azure Active Directory (versão prévia)

A arquitetura de relatórios no Azure Active Directory (Azure AD) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - Os **logs de auditoria** - logs de [auditoria](concept-audit-logs.md) fornecem informações de atividade do sistema sobre gerenciamento de usuários e de grupos, aplicativos gerenciados e atividades de diretório.
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

## <a name="provisioning-logs"></a>Registos de aprovisionamento

Os logs de provisionamento fornecem respostas para as seguintes perguntas:

* Quais grupos foram criados com êxito no ServiceNow?
* Como as funções foram importadas do Amazon Web Services?
* Quais usuários não foram criados com êxito no DropBox?

Você pode acessar os logs de provisionamento selecionando **logs de provisionamento** na seção **monitoramento** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registros de provisionamento sejam exibidos no Portal.

![Logs de provisionamento](./media/concept-provisioning-logs/access-provisioning-logs.png "Registos de aprovisionamento")


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

![Informações detalhadas](./media/concept-provisioning-logs/steps.png "Filtro")


## <a name="filter-provisioning-activities"></a>Filtrar atividades de provisionamento

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de provisionamento usando os campos padrão a seguir. Observe que os valores nos filtros são preenchidos dinamicamente com base em seu locatário. Se, por exemplo, você não tiver nenhum evento de criação em seu locatário, não haverá uma opção de filtro para Create.

- Identidade
- Ação
- Sistema de origem
- Sistema de destino
- Estado
- Date


![Sem](./media/concept-provisioning-logs/filter.png "Filtro")

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
- Intervalo de tempo personalizado

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


![Sem](./media/concept-provisioning-logs/provisioning-tabs.png "Temas")



### <a name="steps"></a>Passos

A guia **etapas** descreve as etapas executadas para provisionar um objeto. O provisionamento de um objeto pode consistir em quatro etapas: 

- Importar objeto
- Determinar se o objeto está no escopo
- Corresponder objeto entre origem e destino
- Provisionar objeto (executar ação-pode ser criar, atualizar, excluir ou desabilitar)



![Sem](./media/concept-provisioning-logs/steps.png "Filtro")


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

## <a name="error-codes"></a>Códigos de Erro

Use a tabela a seguir para entender melhor como resolver erros que podem ser encontrados nos logs de provisionamento. Para quaisquer códigos de erro ausentes, forneça comentários usando o link na parte inferior desta página. 

|Código de erro|Descrição|
|---|---|
|Conflito, EntryConflict|Corrija os valores de atributo conflitantes no Azure AD ou no aplicativo ou revise a configuração de atributo correspondente se a conta de usuário conflitante deveria ser correspondida e assumida. Examine a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) a seguir para obter mais informações sobre como configurar atributos correspondentes.|
|TooManyRequests|O aplicativo de destino rejeitou essa tentativa de atualizar o usuário porque ele está sobrecarregado e recebendo muitas solicitações. Não há nada a fazer. Essa tentativa será desativada automaticamente. A Microsoft também foi notificada desse problema.|
|InternalServerError |O aplicativo de destino retornou um erro inesperado. Pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione. Essa tentativa será desativada automaticamente em 40 minutos.|
|InsufficientRights, MethodNotAllowed, não permitido, não autorizado| O Azure AD foi capaz de autenticar com o aplicativo de destino, mas não foi autorizado a executar a atualização. Examine todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicativo.|
|UnprocessableEntity|O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo de destino pode não estar correta, ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione.|
|WebExceptionProtocolError |Ocorreu um erro de protocolo HTTP durante a conexão com o aplicativo de destino. Não há nada a fazer. Essa tentativa será desativada automaticamente em 40 minutos.|
|InvalidAnchor|Um usuário que foi criado ou correspondido anteriormente pelo serviço de provisionamento não existe mais. Verifique se o usuário existe. Para forçar uma nova correspondência de todos os usuários, use o MS API do Graph para [reiniciar o trabalho](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Observe que a reinicialização do provisionamento disparará um ciclo inicial, o que pode levar tempo para ser concluído. Ele também exclui o cache que o serviço de provisionamento usa para operar, o que significa que todos os usuários e grupos no locatário terão que ser avaliados novamente e determinados eventos de provisionamento poderão ser descartados.|
|Não implementado | O aplicativo de destino retornou uma resposta inesperada. A configuração do aplicativo pode não estar correta ou pode haver um problema de serviço com o aplicativo de destino que está impedindo que isso funcione. Examine todas as instruções fornecidas pelo aplicativo de destino, bem como o respectivo [tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)de aplicativo. |
|MandatoryFieldsMissing, MissingValues |Não foi possível criar o usuário porque os valores necessários estão ausentes. Corrija os valores de atributo ausentes no registro de origem ou examine a configuração de atributo correspondente para garantir que os campos obrigatórios não sejam omitidos. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre como configurar atributos correspondentes.|
|SchemaAttributeNotFound |Não foi possível executar a operação porque foi especificado um atributo que não existe no aplicativo de destino. Consulte a [documentação](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) sobre personalização de atributo e verifique se a configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de provisionamento do Azure AD. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|InvalidDomain |A operação não pôde ser executada devido a um valor de atributo que contém um nome de domínio inválido. Atualize o nome de domínio no usuário ou adicione-o à lista de permitidos no aplicativo de destino. |
|cedido |A operação não pôde ser concluída porque o aplicativo de destino demorou muito tempo para responder. Não há nada a fazer. Essa tentativa será repetida automaticamente em 40 minutos.|
|LicenseLimitExceeded|Não foi possível criar o usuário no aplicativo de destino porque não há licenças disponíveis para esse usuário. Adquira licenças adicionais para o aplicativo de destino ou examine as atribuições de usuário e a configuração de mapeamento de atributo para garantir que os usuários corretos sejam atribuídos com os atributos corretos.|
|DuplicateTargetEntries  |A operação não pôde ser concluída porque mais de um usuário no aplicativo de destino foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado do aplicativo de destino ou RECONFIGURE os mapeamentos de atributo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplicateSourceEntries | A operação não pôde ser concluída porque mais de um usuário foi encontrado com os atributos correspondentes configurados. Remova o usuário duplicado ou RECONFIGURE os mapeamentos de atributo conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Passos seguintes

* [Verificar o status do provisionamento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problema ao configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


