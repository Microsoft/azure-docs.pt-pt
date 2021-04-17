---
title: Visão geral dos registos de provisionamento no portal Azure (pré-visualização) | Microsoft Docs
description: Obtenha uma introdução aos relatórios de registo de provisionamento no Azure Ative Directory através do portal Azure.
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
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 468e885bab6aab4becb5aaaec7b4d52ce5ef5e07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535996"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Visão geral dos registos de provisionamento no portal Azure (pré-visualização)

A arquitetura de reporte no Azure Ative Directory (Azure AD) consiste nos seguintes componentes:

- Atividade: 
    - **Insuposições**: Informações sobre a utilização de aplicações geridas e atividades de inscrição do utilizador.
    - [Registos de auditoria](concept-audit-logs.md): Informações de atividade do sistema sobre gestão de utilizadores e grupos, aplicações geridas e atividades de diretório.
    - **Registos de provisionamento**: Atividade do sistema sobre utilizadores, grupos e funções que são a provisionadas pelo serviço de fornecimento de Azure AD. 

- Segurança: 
    - **Entradas arriscadas**: Um [sinal de risco](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.
    - **Utilizadores sinalizados para o risco**: Um [utilizador arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de utilizador que pode ter sido comprometida.

Este tópico dá-lhe uma visão geral dos registos de provisionamento. Os registos fornecem respostas a questões como: 

* Que grupos foram criados com sucesso no ServiceNow?
* Que utilizadores foram removidos com sucesso da Adobe?
* Que utilizadores do Workday foram criados com sucesso no Ative Directory? 

## <a name="prerequisites"></a>Pré-requisitos

Estes utilizadores podem aceder aos dados em registos de provisionamento:

* Proprietários de aplicações (registos para as suas próprias aplicações)
* Utilizadores nas funções de Administrador de Segurança, Leitor de Segurança, Leitor de Relatórios, Operador de Segurança, Administrador de Aplicações e Administrador de Aplicações cloud
* Utilizadores em uma função personalizada com a [permissão de provisioningLogs](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)
* Administradores globais


Para que possa ver o relatório de atividades de provisionamento, o seu inquilino deve ter uma licença AZure AD Premium associada a ele. Para atualizar a sua edição AD Azure, consulte [Começar com o Azure Ative Directory Premium.](../fundamentals/active-directory-get-started-premium.md) 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>Formas de interagir com os registos de provisionamento 
Os clientes podem interagir com os registos de provisionamento de quatro formas:

- Acedendo aos registos do portal Azure, conforme descrito na secção seguinte.
- Transmitir os registos de provisionamento para [o Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). Este método permite a retenção alargada de dados e a construção de dashboards, alertas e consultas personalizados.
- Consulta da [API do Gráfico microsoft](/graph/api/resources/provisioningobjectsummary) para os registos de provisionamento.
- Descarregando os registos de provisionamento como um ficheiro CSV ou JSON.

## <a name="access-the-logs-from-the-azure-portal"></a>Aceda aos registos a partir do portal Azure
Pode aceder aos registos de provisionamento selecionando **Registos de Provisionamento** na secção de **Monitorização** do painel de **diretório ativo Azure** no [portal Azure](https://portal.azure.com). Pode levar até duas horas para que alguns registos de provisionamento apareçam no portal.

![Screenshot que mostra seleções para aceder a registos de provisionamento.](./media/concept-provisioning-logs/access-provisioning-logs.png "Registos de aprovisionamento")


Um registo de provisionamento tem uma visão de lista padrão que mostra:

- A identidade
- A ação
- O sistema de origem
- O sistema alvo
- O estado
- A data


![Screenshot que mostra colunas predefinidos num registo de provisionamento.](./media/concept-provisioning-logs/default-columns.png "Colunas predefinidos")

Pode personalizar a vista da lista selecionando **Colunas** na barra de ferramentas.

![Screenshot que mostra o botão para personalizar colunas.](./media/concept-provisioning-logs/column-chooser.png "Escolha de coluna")

Esta área permite-lhe exibir campos adicionais ou remover campos que já estão expostos.

![Screenshot que mostra colunas disponíveis com algumas selecionadas.](./media/concept-provisioning-logs/available-columns.png "Colunas disponíveis")

Selecione um item na vista da lista para obter informações mais detalhadas.

![Screenshot que mostra informações detalhadas.](./media/concept-provisioning-logs/steps.png "Filtro")


## <a name="filter-provisioning-activities"></a>Atividades de provisão de filtros

Pode filtrar os seus dados de atenção. Alguns valores de filtro são dinamicamente povoados com base no seu inquilino. Se, por exemplo, não tiver eventos de "criar" no seu inquilino, não haverá uma opção de filtro **Create.**

Na vista predefinitiva, pode selecionar os seguintes filtros:

- **Identidade**
- **Data**
- **Estado**
- **Ação**


![Screenshot que mostra valores de filtro.](./media/concept-provisioning-logs/default-filter.png "Filtro")

O filtro **identidade** permite especificar o nome ou a identidade que lhe interessa. Esta identidade pode ser um utilizador, grupo, papel ou outro objeto. 

Pode pesquisar pelo nome ou identificação do objeto. O ID varia por cenário. Por exemplo, quando está a aatar um objeto de Azure AD à Salesforce, o ID de origem é o ID do objeto do utilizador em Azure AD. O ID-alvo é o ID do utilizador na Salesforce. Quando se fornece de Workday a Ative Directory, a identificação de fonte é a identificação do trabalhador do workday. 

> [!NOTE]
> O nome do utilizador pode nem sempre estar presente na coluna **Identidade.** Haverá sempre uma identificação. 


O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos. Os valores possíveis são:

- 1 mês
- 7 dias
- 30 dias
- 24 horas
- Intervalo de tempo personalizado

Quando selecionar um prazo personalizado, pode configurar uma data de início e uma data de fim.

O filtro **'Estado'** permite-lhe selecionar:

- **Todos**
- **Com êxito**
- **Falha**
- **Ignorado**

O filtro **Action** permite filtrar estas ações:

- **Criar** 
- **Atualizar**
- **Eliminar**
- **Desativar**
- **Outro**

Além dos filtros da vista predefinida, pode definir os seguintes filtros.

![Screenshot que mostra campos que pode adicionar como filtros.](./media/concept-provisioning-logs/add-filter.png "Escolha um campo")

- **ID de trabalho**: Um ID de trabalho único está associado a cada aplicação que você habilitado a provisão.   

- **ID do ciclo**: O ID do ciclo identifica exclusivamente o ciclo de provisionamento. Pode partilhar este ID com suporte ao produto para consultar o ciclo em que este evento ocorreu.

- **Alterar ID**: O ID de alteração é um identificador único para o evento de provisionamento. Você pode compartilhar este ID com suporte do produto para procurar o evento de provisionamento.   

- **Sistema de Origem**: Pode especificar de onde a identidade está a ser fornecida. Por exemplo, quando está a aatar um objeto do AD AD a ServiceNow, o sistema de origem é Azure AD. 

- **Sistema alvo**: Pode especificar para onde a identidade está a ser a provisionada. Por exemplo, quando está a aatar um objeto do AD AD a ServiceNow, o sistema-alvo é o ServiceNow. 

- **Aplicação**: Só pode apresentar registos de aplicações com um nome de visor que contenha uma cadeia específica.

## <a name="provisioning-details"></a>Provisionamento de detalhes 

Ao selecionar um item na vista da lista de provisionamento, obtém mais detalhes sobre este item. Os detalhes são agrupados nos seguintes separadores.

![Screenshot que mostra quatro separadores que contêm detalhes de provisionamento.](./media/concept-provisioning-logs/provisioning-tabs.png "Separadores")

- **Passos**: Delineia as medidas tomadas para a disponibilização de um objeto. O provisionamento de um objeto pode consistir em quatro etapas:
  
  1. Importe o objeto.
  1. Determinar se o objeto está no âmbito.
  1. Combine o objeto entre a fonte e o alvo.
  1. Forrar o objeto (criar, atualizar, eliminar ou desativar).

  ![A screenshot mostra os passos de provisionamento no separador Passos.](./media/concept-provisioning-logs/steps.png "Filtro")

- **Resolução de problemas & Recomendações**: Fornece o código de erro e a razão. A informação de erro só está disponível se uma falha acontecer.

- **Propriedades Modificadas**: Mostra o valor antigo e o novo valor. Se não há valor antigo, esta coluna está em branco.

- **Resumo**: Fornece uma visão geral do que aconteceu e identifica o objeto nos sistemas de origem e alvo.

## <a name="download-logs-as-csv-or-json"></a>Baixar registos como CSV ou JSON

Pode descarregar os registos de provisionamento para utilização posterior, indo para os registos no portal Azure e selecionando **Download**. O ficheiro será filtrado com base nos critérios de filtro selecionados. Torne os filtros o mais específicos possível para reduzir o tamanho e a hora do download. 

O download do CSV inclui três ficheiros:

* **ProvisioningLogs**: Descarrega todos os registos, exceto as etapas de provisionamento e propriedades modificadas.
* **ProvisioningLogs_ProvisioningSteps**: Contém os passos de provisionamento e o ID de alteração. Pode utilizar o ID de alteração para se juntar ao evento com os outros dois ficheiros.
* **ProvisioningLogs_ModifiedProperties**: Contém os atributos que foram alterados e o ID de alteração. Pode utilizar o ID de alteração para se juntar ao evento com os outros dois ficheiros.

#### <a name="open-the-json-file"></a>Abra o ficheiro JSON
Para abrir o ficheiro JSON, utilize um editor de texto como [o Microsoft Visual Studio Code](https://aka.ms/vscode). O Código do Estúdio Visual facilita a leitura do ficheiro fornecendo destaque de sintaxe. Também pode abrir o ficheiro JSON utilizando navegadores num formato não editado, como o [Microsoft Edge.](https://aka.ms/msedge) 

#### <a name="prettify-the-json-file"></a>Prettificar o ficheiro JSON
O ficheiro JSON é descarregado em formato minificado para reduzir o tamanho do download. Este formato pode dificultar a leitura da carga útil. Confira duas opções para preentificar o ficheiro:

- Utilize [o Código do Estúdio Visual para formatar o JSON](https://code.visualstudio.com/docs/languages/json#_formatting).

- Utilize o PowerShell para formatar o JSON. Este script irá descodui o JSON num formato que inclui separadores e espaços: 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>Parse o ficheiro JSON

Aqui estão alguns comandos de amostra para trabalhar com o ficheiro JSON utilizando o PowerShell. Pode usar qualquer linguagem de programação com a qual se sinta confortável.  

Primeiro, [leia o ficheiro JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) executando este comando:

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

Agora pode analisar os dados de acordo com o seu cenário. Veja a seguir alguns exemplos: 

- Produção de todas as IDs de trabalho no ficheiro JSON:

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- Todas as iDs de alteração para eventos onde a ação foi "criar":

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>O que deve saber

Aqui ficam algumas dicas e considerações para os relatórios de provisionamento:

- As lojas do portal Azure reportaram dados de provisionamento durante 30 dias se tiver uma edição premium e 7 dias se tiver uma edição gratuita. Pode publicar os registos de provisão para [Registar Analytics](../app-provisioning/application-provisioning-log-analytics.md) para retenção além de 30 dias. 

- Pode utilizar o atributo de ID de alteração como identificador único. Isto é útil quando está a interagir com o suporte do produto, por exemplo.

- Pode ver eventos ignorados para utilizadores que não estão no âmbito. Isto é esperado, especialmente quando o âmbito de sincronização é definido para todos os utilizadores e grupos. O serviço avaliará todos os objetos do arrendatário, mesmo os que estão fora de alcance. 

- Os registos de provisionamento estão atualmente indisponíveis na nuvem governamental. Se não conseguir aceder aos registos de provisionamento, utilize os registos de auditoria como uma solução temporária. 

- Os registos de provisionamento não mostram importações de funções (aplica-se à AWS, Salesforce e Zendesk). Pode encontrar os registos de importações de funções nos registos de auditoria. 

## <a name="error-codes"></a>Códigos de erro

Utilize a tabela seguinte para entender melhor como resolver os erros que encontra nos registos de provisionamento. Para quaisquer códigos de erro que faltem, forneça feedback utilizando o link na parte inferior desta página. 

|Código de erro|Description|
|---|---|
|Conflito, Conflito de Entrada|Corrija os valores de atributos contraditórios em AZure AD ou na aplicação. Ou, reveja a configuração do seu atributo correspondente se a conta de utilizador conflituosa deveria ser correspondida e assumida. Reveja a [documentação](../app-provisioning/customize-application-attributes.md) para obter mais informações sobre a configuração de atributos correspondentes.|
|TooManyRequests|A aplicação-alvo rejeitou esta tentativa de atualizar o utilizador por estar sobrecarregado e receber demasiados pedidos. Não há nada para fazer. Esta tentativa será automaticamente retirada. A Microsoft também foi notificada deste problema.|
|InternalServerError |A aplicação-alvo devolveu um erro inesperado. Um problema de serviço com a aplicação-alvo pode estar a impedir que isto funcione. Esta tentativa será automaticamente retirada em 40 minutos.|
|Direitos insuficientes, MétodoNotAllowed, Não Autorizado, Não Autorizado| A Azure AD autenticou-se com a aplicação-alvo, mas não estava autorizada a realizar a atualização. Reveja todas as instruções que o pedido-alvo forneceu, juntamente com o respetivo [tutorial de](../saas-apps/tutorial-list.md)aplicação .|
|Entidade Não Processável|O pedido de destino devolveu uma resposta inesperada. A configuração da aplicação-alvo pode não estar correta, ou um problema de serviço com a aplicação-alvo pode estar a impedir que isso funcione.|
|WebExceptionProtocolError |Ocorreu um erro de protocolo HTTP na ligação à aplicação-alvo. Não há nada a fazer. Esta tentativa será automaticamente retirada em 40 minutos.|
|InvalidAnchor|Um utilizador que tenha sido previamente criado ou correspondido pelo serviço de fornecimento já não existe. Certifique-se de que o utilizador existe. Para forçar uma nova correspondência de todos os utilizadores, utilize a API do Microsoft Graph para [reiniciar o trabalho](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). <br><br>O reinício do provisionamento irá desencadear um ciclo inicial, que pode levar algum tempo a ser concluído. O reinício do fornecimento também elimina a cache que o serviço de fornecimento utiliza para funcionar. Isto significa que todos os utilizadores e grupos do arrendatário terão de ser novamente avaliados, e certos eventos de provisionamento poderão ser eliminados.|
|Não ÉDopliizado | A aplicação-alvo devolveu uma resposta inesperada. A configuração da aplicação pode não estar correta, ou um problema de serviço com a aplicação alvo pode estar a impedir que isso funcione. Reveja todas as instruções que o pedido-alvo forneceu, juntamente com o respetivo [tutorial de](../saas-apps/tutorial-list.md)aplicação . |
|Aeródromos ObrigatóriasMissing, MissingValues |O utilizador não pôde ser criado porque faltam valores necessários. Corrija os valores de atributos em falta no registo de origem ou reveja a configuração do seu atributo correspondente para garantir que os campos necessários não sejam omitidos. [Saiba mais](../app-provisioning/customize-application-attributes.md) sobre configurar atributos correspondentes.|
|SchemaAttributeNotFound |A operação não pôde ser realizada porque foi especificado um atributo que não existe na aplicação-alvo. Consulte a [documentação](../app-provisioning/customize-application-attributes.md) sobre a personalização do atributo e certifique-se de que a sua configuração está correta.|
|InternalError |Ocorreu um erro de serviço interno no serviço de fornecimento de Azure AD. Não há nada a fazer. Esta tentativa será automaticamente novamente julgada em 40 minutos.|
|InvalidDomain |A operação não pôde ser realizada porque um valor de atributo contém um nome de domínio inválido. Atualize o nome de domínio no utilizador ou adicione-o à lista permitida na aplicação-alvo. |
|Tempo Limite |A operação não pôde ser concluída porque a aplicação do alvo demorou muito tempo a responder. Não há nada a fazer. Esta tentativa será automaticamente novamente julgada em 40 minutos.|
|LicençaLimitExceed|O utilizador não pôde ser criado na aplicação-alvo porque não existem licenças disponíveis para este utilizador. Obtenha mais licenças para o pedido de destino. Ou, reveja as suas atribuições de utilizador e atribua a configuração de mapeamento para garantir que os utilizadores corretos são atribuídos com os atributos corretos.|
|DuplicateTargets  |A operação não pôde ser concluída porque mais de um utilizador na aplicação-alvo foi encontrado com os atributos de correspondência configurados. Remova o utilizador duplicado da aplicação-alvo ou [reconfigure os mapeamentos do seu atributo](../app-provisioning/customize-application-attributes.md).|
|DuplicateSourceEntries | A operação não pôde ser concluída porque mais de um utilizador foi encontrado com os atributos correspondentes configurados. Remova o utilizador duplicado ou [reconfigure os mapeamentos do seu atributo](../app-provisioning/customize-application-attributes.md).|
|ImportSkipped | Quando cada utilizador é avaliado, o sistema tenta importar o utilizador do sistema de origem. Este erro ocorre geralmente quando o utilizador que está a ser importado está a perder a propriedade correspondente definida nos mapeamentos do seu atributo. Sem um valor presente no objeto do utilizador para o atributo correspondente, o sistema não pode avaliar alterações de scoping, correspondência ou exportação. Note que a presença deste erro não indica que o utilizador esteja no âmbito, uma vez que ainda não avaliou a verificação para o utilizador.|
|EntradaSsynchronizationSkipped | O serviço de fornecimento solicitou com sucesso o sistema de origem e identificou o utilizador. Não foram tomadas mais medidas contra o utilizador e foram ignoradas. O utilizador pode ter estado fora de alcance, ou o utilizador pode já ter existido no sistema alvo, sem necessidade de mais alterações.|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| Um pedido GET para recuperar um utilizador ou grupo recebeu vários utilizadores ou grupos na resposta. O sistema espera receber apenas um utilizador ou grupo na resposta. [Por exemplo,](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)se fizer um pedido GET para recuperar um grupo e fornecer um filtro para excluir membros, e o seu ponto final de gestão de identidade de domínio cruzado (SCIM) retorna os membros, obterá este erro.|

## <a name="next-steps"></a>Passos seguintes

* [Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [Problema de configuração do fornecimento de um utilizador a uma aplicação da Galeria AD Azure](../app-provisioning/application-provisioning-config-problem.md)
* [API de gráfico para provisionamento de registos](/graph/api/resources/provisioningobjectsummary)