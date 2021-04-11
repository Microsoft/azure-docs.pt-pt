---
title: 'Azure AD Connect Single Object Sync '
description: Aprenda a sincronizar um objeto do Ative Directory para a Azure AD para resolução de problemas.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726096"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect Single Object Sync 

A ferramenta Azure AD Connect Single Object Sync é um cmdlet PowerShell que pode ser usado para sincronizar um objeto individual de Ative Directory a Azure Ative Directory. O relatório gerado pode ser usado para investigar e resolver problemas por problemas de sincronização de objetos. 

> [!NOTE]
> A ferramenta suporta a sincronização do Ative Directory para o Azure Ative Directory. Não suporta a sincronização do Azure Ative Directory para o Ative Directory. 
>
> A ferramenta suporta sincronizar um objeto modificar adicionar e atualizar. Não suporta sincronizar uma modificação do objeto Eliminar. 

## <a name="how-it-works"></a>Como funciona
A ferramenta Single Object Sync requer um nome distinto do Ative Directory como entrada para encontrar o conector de origem e a divisória para importação. Exporta as alterações para o Azure Ative Directory. A ferramenta gera uma saída JSON semelhante ao tipo de recurso **De provisionamentoObjectSummary.** 

A ferramenta Single Object Sync executa os seguintes passos: 

 1. Determine se o domínio (fonte) do objeto (Ative Directory Connector e Partition) está no âmbito de sincronização. 
 2. Determine se o domínio (alvo) do Objeto (Azure Ative Directory Connector e Partition) está no âmbito de sincronização. 
 3. Determine se a Unidade Organizacional do Objeto está em sincronização. 
 4. Determine se o Objeto está acessível usando credenciais de conta de conector. 
 5. Determine se o tipo de objeto está no âmbito de sincronização. 
 6. Determine se o Objeto está no âmbito de sincronização se a filtragem do grupo estiver ativada. 
 7. Objeto de importação do Diretório Ativo para o Espaço de Conector de Diretório Ativo. 
 8. Objeto de importação de Azure Ative Directory para Azure Ative Directory Connector Space. 
 9. Sync Object do Ative Directory Connector Space. 
 10. Objeto de exportação do Espaço do Conector ativo Azure para o Diretório Ativo Azure. 

Além da saída JSON, a ferramenta gera um relatório HTML que tem todos os detalhes da operação de sincronização. O relatório HTML está localizado em **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> .htm**. Este relatório HTML pode ser partilhado com a equipa de apoio para fazer mais resolução de problemas, se necessário. 

O relatório HTML tem o seguinte: 

|Tecla de Tabulação|Description|
|-----|-----|
|Passos|Descreve os passos dados para sincronizar um objeto. Cada passo contém detalhes para a resolução de problemas. As etapas de Importação, Sincronização e Exportação contêm informações adicionais de atributos, tais como nome, é multi-valor, tipo, valor, valor acrescentado, exclusão de valor, operação, regra de sincronização, tipo de mapeamento e fonte de dados.| 
|Recomendação de & de resolução de problemas|Fornece o código de erro e a razão. A informação de erro só está disponível se uma falha acontecer.| 
|Propriedades Modificadas|Mostra o valor antigo e o novo valor. Se não houver valor antigo ou se o novo valor for apagado, essa célula está em branco. Para atributos multivalorizado mostra a contagem. O nome do atributo é um link para separador Passos: Objeto de exportação do Azure Ative Directory Connector Space para Azure Ative Directory: Attribute Info que contém detalhes adicionais do atributo como o nome, é multi-valor, tipo, valor, valor acrescentado, exclusão de valor, operação, regra de sincronização, tipo de mapeamento e fonte de dados.| 
|Resumo|Fornece uma visão geral do que aconteceu e identifica o objeto nos sistemas de origem e alvo.| 

## <a name="prerequisites"></a>Pré-requisitos 

Para utilizar a ferramenta Single Object Sync, terá de utilizar o lançamento de março de 2021 do Azure AD Connect ou mais tarde. 

### <a name="run-the-single-object-sync-tool"></a>Executar a ferramenta Single Object Sync 

Para executar a ferramenta Single Object Sync, execute os seguintes passos: 

 1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção Executar como Administrador. 

 2. Desaprova a política de [execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) para RemoteSigned ou Sem Restrições. 

 3. Desative o programador de sincronização depois de verificar se não estão a ser operações de sincronização. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importar o módulo de Diagnóstico AdSync 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Invoque o cmdlet Single Object Sync. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Re-activar o Programador de Sincronização. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Parâmetros de entrada de sincronização de objeto único|Description| 
|-----|----|
|Nome distinto|Este é um parâmetro de corda necessário. </br></br>Este é o nome distinto do objeto Ative Directory que precisa de sincronização e resolução de problemas.| 
|Fase de realização|Este é um parâmetro de comutação opcional.</br></br>Este parâmetro pode ser utilizado para evitar a exportação das alterações ao Diretório Ativo do Azure.</br></br>**Nota:** O cmdlet es conseguinte. </br></br>**Nota:** O servidor Azure AD Connect Staging não exportará as alterações para o Azure Ative Directory.|
|NoHtmlReport|Este é um parâmetro de comutação opcional.</br></br>Este parâmetro pode ser utilizado para evitar a geração do relatório HTML. 

## <a name="single-object-sync-throttling"></a>Estrangulamento de sincronização de objeto único 

A ferramenta Single Object Sync **destina-se** a investigar e resolver problemas por problemas de sincronização de objetos. Não **se** destina a substituir o ciclo de sincronização executado pelo Programador. A importação do Azure Ative Directory e a exportação para o Azure Ative Directory estão sujeitas a limites de estrangulamento. Por favor, reda o tempo após 5 minutos, se atingir o limite de estrangulamento. 

## <a name="next-steps"></a>Passos seguintes
- [Sincronização de objetos de resolução de problemas](tshoot-connect-objectsync.md)
- [Objeto de resolução de problemas não sincronizado](tshoot-connect-object-not-syncing.md)
- [Resolução de problemas de ponta a ponta de objetos e atributos AZure AD Connect](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
