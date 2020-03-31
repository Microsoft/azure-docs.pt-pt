---
title: File Integrity Monitoring in Azure Security Center (Monitorização da Integridade dos Ficheiros no Centro de Segurança do Azure)
description: Aprenda a comparar as linhas de base com a Monitorização da Integridade do Ficheiro no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664414"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparar linhas de base com a Monitorização d a Integridade dos Ficheiros (FIM)

A Monitorização da Integridade do Ficheiro (FIM) informa-o quando ocorrerem alterações em áreas sensíveis dos seus recursos, para que possa investigar e tratar de atividades não autorizadas. O FIM monitoriza ficheiros Windows, registos windows e ficheiros Linux.

Este tópico explica como ativar o FIM nos ficheiros e registos. Para mais informações sobre o FIM, consulte [a Monitorização da Integridade do Ficheiro no Centro](security-center-file-integrity-monitoring.md)de Segurança Azure .

## <a name="why-use-fim"></a>Por que usar o FIM?

Sistema operativo, aplicações e configurações associadas controlam o comportamento e o estado de segurança dos seus recursos. Por isso, os atacantes visam os ficheiros que controlam os seus recursos, de forma a ultrapassar o sistema operativo de um recurso e/ou executar atividades sem serem detetados.

De facto, muitas normas de conformidade regulamentar, tais como o PCI-DSS & ISO 17799, exigem a implementação dos controlos DO.  

## <a name="enable-built-in-recursive-registry-checks"></a>Permitir controlos de registo recursivos incorporados

Os incumprimentos da colmeia de registo FIM fornecem uma forma conveniente de monitorizar alterações recursivas dentro das áreas de segurança comuns.  Por exemplo, um adversário pode configurar um script para executar em LOCAL_SYSTEM contexto configurando uma execução no arranque ou encerramento.  Para monitorizar alterações deste tipo, ative a verificação incorporada.  

![Registo](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> As verificações recursivas aplicam-se apenas às colmeias de segurança recomendadas e não às vias de registo personalizadas.  

## <a name="adding-a-custom-registry-check"></a>Adicionar uma verificação de registo personalizado

As linhas de base do FIM começam por identificar características de um estado conhecido para o sistema operativo e aplicação de suporte.  Para este exemplo, vamos focar-nos nas configurações da política de passwords para o Windows Server 2008 e superior.


|Nome da Política                 | Definição de Registo|
|---------------------------------------|-------------|
|Controlador de domínio: recusar as alterações de palavra-passe de conta de computador| MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\RefusePasswordChange|
|Membro de domínio: encriptar digitalmente ou assinar dados de canal seguro (sempre)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\RequiresignOrSeal|
|Membro de domínio: encriptar digitalmente dados de canal seguro (quando possível)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\SealSecureChannel|
|Membro de domínio: assinar digitalmente dados de canal seguro (quando possível)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\SignSecureChannel|
|Membro de domínio: desativar as alterações de palavra-passe de conta de computador|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\DisablePasswordChange|
|Membro de domínio: idade máxima de palavra-passe de conta de computador|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\MaximumPasswordAge|
|Membro de domínio: requerer chave de sessão forte (Windows 2000 ou posterior)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\RequireStrongKey|
|Segurança da rede: Restringir ntLM: autenticação NTLM neste domínio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\RestrictNTLMInDomain|
|Segurança de rede: restringir NTLM: adicionar exceções de servidor neste domínio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\DCAllowedNTLMServers|
|Segurança da rede: Restringir ntLM: Autenticação NTLM auditada neste domínio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parâmetros\AuditNTLMInDomain|

> [!NOTE]
> Para saber mais sobre as definições de registo suportadas por várias versões do sistema operativo, consulte a folha de cálculo de [referência definições](https://www.microsoft.com/download/confirmation.aspx?id=25250)de definições de políticas do grupo .

*Para configurar o FIM para monitorizar as linhas de base do registo:*

1. No **Registo do Registo** do Windows para alterar a janela, na caixa de texto da chave do **registo do Windows,** introduza a tecla de registo.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Ativar o FIM num registo](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Rastrear alterações nos ficheiros Windows

1. No **Ficheiro Adicionar Windows para Alterar A** janela de rastreio, na caixa de texto do caminho de **entrada,** introduza a pasta que contém os ficheiros que pretende rastrear. No exemplo da seguinte figura, a **Aplicação Web Contoso** reside no D:\ conduzir dentro da estrutura da pasta **ContosWebApp.**  
1. Crie uma entrada personalizada de ficheiros Windows fornecendo um nome da classe de definição, permitindo a recursão e especificando a pasta superior com um sufixo wildcard (*).

    ![Ativar o FIM num ficheiro](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Recuperação de dados de alteração

Os dados de monitorização da integridade do ficheiro residem dentro do conjunto de tabelas Azure Log Analytics / ConfigurationChange.  

 1. Detete um intervalo de tempo para recuperar um resumo das alterações por recurso.
No exemplo seguinte, estamos a recuperar todas as alterações nos últimos catorze dias nas categorias de registo e ficheiros:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Para ver detalhes das alterações do registo:

    1. Remover **Ficheiros** da cláusula **onde,** 
    1. Retire a linha de resumição e substitua-a por uma cláusula de encomenda:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Os relatórios podem ser exportados para cSV para arquivo e/ou canalizados para um relatório power BI.  

![Dados DO FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)