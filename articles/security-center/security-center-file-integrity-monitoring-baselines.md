---
title: File Integrity Monitoring in Azure Security Center (Monitorização da Integridade dos Ficheiros no Centro de Segurança do Azure)
description: Saiba como comparar linhas de base com a Monitorização da Integridade do Ficheiro no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634715"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparar linhas de base com a Monitorização d a Integridade dos Ficheiros (FIM)

O Monitor de Integridade do Ficheiro (FIM) informa-o quando ocorrem alterações em áreas sensíveis dos seus recursos, para que possa investigar e tratar de atividades não autorizadas. FIM monitoriza ficheiros Windows, registos windows e ficheiros Linux.

Este tópico explica como ativar o FIM nos ficheiros e registos. Para obter mais informações sobre o FIM, consulte [a Monitorização da Integridade do Ficheiro no Centro de Segurança Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Porquê usar o FIM?

Sistema operativo, aplicações e configurações associadas controlam o comportamento e o estado de segurança dos seus recursos. Por isso, os atacantes direcionam os ficheiros que controlam os seus recursos, de forma a ultrapassar o sistema operativo de um recurso e/ou executar atividades sem serem detetados.

De facto, muitas normas de conformidade regulamentar, como a PCI-DSS & ISO 17799, exigem a implementação dos controlos FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Permitir verificações de registos recursivos incorporados

Os predefinições da colmeia de registo FIM fornecem uma forma conveniente de monitorizar as alterações recursivas nas áreas de segurança comuns.  Por exemplo, um adversário pode configurar um script para executar em LOCAL_SYSTEM contexto, configurando uma execução no arranque ou encerramento.  Para monitorizar as alterações deste tipo, ative o controlo incorporado.  

![Registo](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Os controlos recursivos aplicam-se apenas às colmeias de segurança recomendadas e não aos caminhos de registo personalizados.  

## <a name="add-a-custom-registry-check"></a>Adicione uma verificação de registo personalizado

As linhas de base FIM começam por identificar características de um estado de bem-estar conhecido para o sistema operativo e aplicação de suporte.  Para este exemplo, vamos focar-nos nas configurações de política de palavra-passe para o Windows Server 2008 e mais alto.


|Nome da Política                 | Definição de registo|
|---------------------------------------|-------------|
|Controlador de domínio: recusar as alterações de palavra-passe de conta de computador| MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\RefusePasswordChange|
|Membro de domínio: encriptar digitalmente ou assinar dados de canal seguro (sempre)|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\RequireSignOrSeal|
|Membro de domínio: encriptar digitalmente dados de canal seguro (quando possível)|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\SealSecureChannel|
|Membro de domínio: assinar digitalmente dados de canal seguro (quando possível)|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\SignSecureChannel|
|Membro de domínio: desativar as alterações de palavra-passe de conta de computador|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\DisablePasswordS|
|Membro de domínio: idade máxima de palavra-passe de conta de computador|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\MaximumPasswordAge|
|Membro de domínio: requerer chave de sessão forte (Windows 2000 ou posterior)|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\RequireStrongKey|
|Segurança da rede: Restringir a autenticação NTLM neste domínio|MÁQUINA\Sistema\CurrentControlSet\Services \Netlogon\Parâmetros\RestrictNTLMInDomain|
|Segurança de rede: restringir NTLM: adicionar exceções de servidor neste domínio|MÁQUINA\Sistema\CurrentControlSet\Services \Netlogon\Parâmetros\DCAllowedNTLMServers|
|Segurança da rede: Restringir NTLM: Autenticação NTLM auditada neste domínio|MÁQUINA\Sistema\CurrentControlSet\Serviços \Netlogon\Parâmetros\AuditNTLMInDomain|

> [!NOTE]
> Para saber mais sobre as definições de registo suportadas por várias versões do sistema operativo, consulte a folha de cálculo de [referência de Definições de Política de Grupo](https://www.microsoft.com/download/confirmation.aspx?id=25250).

Para configurar o FIM para monitorizar as linhas de base de registo:

1. Na janela de registo do Windows Add Windows para alterar o **rastreio,** na caixa de texto chave do registo do **Windows,** introduza a seguinte chave de registo:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Ativar o FIM num registo":::

## <a name="track-changes-to-windows-files"></a>Rastrear alterações nos ficheiros do Windows

1. Na janela 'Adicionar ficheiro **do Windows' para alterar o rastreio,** na caixa de texto **do caminho 'Introduzir',** introduza a pasta que contém os ficheiros que pretende rastrear. No exemplo na seguinte figura, a **Contoso Web App** reside no D:\ unidade dentro da estrutura da pasta **ContosWebApp.**  
1. Crie uma entrada personalizada de ficheiros Windows fornecendo um nome da classe de definição, permitindo a recursão e especificando a pasta superior com um sufixo wildcard (*).

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Ativar o FIM num ficheiro":::

## <a name="retrieve-change-data"></a>Recuperar dados de alteração

Os dados de monitorização da integridade do ficheiro residem dentro do conjunto de tabelaS Azure Log Analytics / ConfigurationChange.  

 1. Desaprote um intervalo de tempo para recuperar um resumo das alterações por recurso.
No exemplo seguinte, estamos a recuperar todas as alterações nos últimos catorze dias nas categorias de registo e ficheiros:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Para ver detalhes das alterações ao registo:

    1. Remover **ficheiros** da cláusula **de onde,** 
    1. Retire a linha de resumo e substitua-a por uma cláusula de encomenda:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Os relatórios podem ser exportados para CSV para arquivo e/ou canalados para um relatório do Power BI.  

![Dados fim](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)