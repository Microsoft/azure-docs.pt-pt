---
title: Comparar as linhas de base com a monitorização da integridade de ficheiros no Centro de segurança do Azure | Documentos da Microsoft
description: Saiba como comparar as linhas de base com a monitorização da integridade de ficheiros no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358442"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparar as linhas de base usando o monitoramento de integridade de ficheiros (FIM)

Monitoramento de integridade de ficheiros (FIM) informa-o quando as alterações ocorrem a áreas confidenciais nos seus recursos, para que possa investigar e resolver a atividade não autorizada. FIM monitora arquivos do Windows, registos do Windows e ficheiros do Linux.

Este tópico explica como ativar a FIM de nos ficheiros e registos. Para obter mais informações sobre o FIM, consulte [monitorização da integridade de ficheiros no Centro de segurança do Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Porquê utilizar o FIM?

Sistema operacional, aplicativos e configurações associadas controlam o estado de segurança e comportamento dos seus recursos. Por conseguinte, os atacantes visam os ficheiros que controlam os recursos, de modo a overtake o sistema operativo de um recurso e/ou executar atividades sem a ser detetado.

Na verdade, muitos padrões de conformidade a normas, como PCI-DSS e ISO 17799 exigem a implementação de controlos FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Ativar verificações de registro de recursiva incorporada

As predefinições de hive do Registro FIM proporcionam uma forma conveniente para monitorar as alterações de recursiva dentro de áreas de segurança comuns.  Por exemplo, um adversário poderá configurar um script para executar no contexto LOCAL_SYSTEM através da configuração de uma execução na inicialização ou desligamento.  Para monitorizar as alterações deste tipo, ative a verificação incorporada.  

![Registo](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Verificações de recursiva aplicam-se apenas a hives de segurança recomendado e não para caminhos de registo personalizado.  

## <a name="adding-a-custom-registry-check"></a>Adicionar uma verificação de registo personalizado

Linhas de base do FIM comece por identificar características de um Estado de boa conhecida para o sistema operacional e aplicativo de suporte.  Neste exemplo, nos concentraremos nas configurações de política de palavra-passe do Windows Server 2008 ou superior.


|Nome da Política                 | Definição de registo|
|---------------------------------------|-------------|
|Controlador de domínio: Recusar as alterações de palavra-passe de conta de computador| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Membro de domínio: Encriptar digitalmente ou assinar dados de canal seguro (sempre)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Membro de domínio: Encriptar digitalmente dados de canal seguro (quando possível)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Membro de domínio: Digitalmente o início de sessão proteger os dados de canal (quando possível)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Membro de domínio: Desativar as alterações de palavra-passe de conta de computador|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Membro de domínio: Idade de palavra-passe de conta de computador máximo|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Membro de domínio: Exigir chave de sessão forte (Windows 2000 ou posterior)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Segurança de rede: Restringir NTLM:  Autenticação NTLM neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Segurança de rede: Restringir NTLM: Adicionar exceções de servidor neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Segurança de rede: Restringir NTLM: Auditar autenticação NTLM neste domínio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Para saber mais sobre as definições de registo suportadas por várias versões de sistema operativo, consulte a [folha de cálculo de referência de definições de política de grupo](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Para configurar o FIM para monitorizar linhas de base do registo:*

1. Na **adicionar registo do Windows para controlo de alterações** janela, na **chave de registo do Windows** texto, introduza a chave de registo.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Ativar a FIM de um registo](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Registar alterações aos ficheiros do Windows

1. Na **adicionar ficheiro do Windows para controlo de alterações** janela, na **Enter caminho** texto, introduza a pasta que contém os ficheiros que pretende controlar. No exemplo na figura a seguir **aplicação Web da Contoso** reside no D:\ unidade dentro de **ContosWebApp** estrutura de pastas.  
1. Crie uma entrada de arquivo do Windows personalizada ao fornecer um nome de classe de configuração, permitindo a recursão e especificar a pasta principal com um sufixo de caráter universal (*).

    ![Ativar FIM num ficheiro](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>A obter dados de alteração

Monitorização da integridade de dados residem no Azure Log Analytics de ficheiros / tabela ConfigurationChange conjuntos.  

 1. Defina um intervalo de tempo para obter um resumo das alterações ao recurso.
No exemplo a seguir, estamos recuperando todas as alterações nos últimos 14 dias nas categorias de registro e arquivos:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Para ver os detalhes das alterações de registo:

    1. Remover **arquivos** partir do **onde** cláusula, 
    1. Remova a linha de resumo e substitua-o com uma cláusula de ordenação:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Relatórios podem ser exportados para CSV para arquivamento e/ou canalizadas um relatório do Power BI.  

![Data FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)