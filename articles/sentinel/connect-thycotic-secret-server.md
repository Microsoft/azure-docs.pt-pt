---
title: Ligue o servidor secreto thycórmico ao Azure Sentinel| Microsoft Docs
description: Aprenda a usar o conector de dados do Servidor Secreto Thycóctico para puxar os registos do Servidor Secreto Thycórmico no Azure Sentinel. Veja os dados do Servidor Secreto de Timótico nos livros, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567934"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Ligue o seu Servidor Secreto Thycórmico ao Azure Sentinel

> [!IMPORTANT]
> O conector Timoctic Secret Server encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu aparelho Do Servidor Secreto Thycotic ao Azure Sentinel. O conector de dados do Servidor Secreto Thycotic permite-lhe ligar facilmente os seus registos do Servidor Secreto Thycotic com o Azure Sentinel, para que possa ver os dados em livros, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre o Thycotic e o Azure Sentinel faz uso do Conector de Dados CEF para analisar e exibir corretamente mensagens Syslog do Servidor Secreto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no seu espaço de trabalho Azure Sentinel.

- Deve ter lido permissões para partilhar chaves para o espaço de trabalho.

- O seu Servidor Secreto Thycórmico deve ser configurado para exportar registos via Syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Envie registos do Servidor Secreto Timótico para Azure Sentinel

Para obter os seus logins no Azure Sentinel, configuure o seu Servidor Secreto Thycórmico para enviar mensagens Syslog em formato CEF para o seu servidor de reencaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Este servidor terá o agente Log Analytics instalado nele, e o agente encaminha os registos para o seu espaço de trabalho Azure Sentinel.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Na galeria de **conectores de dados,** selecione **o Servidor Secreto Thycotic (Pré-visualização)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções no separador **Instruções,** na **Configuração:**

    1. Menos **de 1. Configuração do agente Linux Syslog** - Faça este passo se ainda não tiver um reencaminhador de log em execução, ou se precisar de outro. Ver [PASSO 1: Desloque o reencaminhador](connect-cef-agent.md) de registo na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

    1. Menos **de 2. Forward Common Event Format (CEF) registra para o agente Syslog** - Siga as instruções da Thycóptica para [configurar o Servidor Secreto](https://thy.center/ss/link/syslog). Esta configuração deve incluir os seguintes elementos:
        - Destino de registo - o nome de anfitrião e/ou endereço IP do seu servidor de reencaminhamento de registo
        - Protocolo e porta – **TCP 514** (se recomendado de outra forma, certifique-se de fazer a alteração paralela no daemon do syslog no seu servidor de reencaminhamento de log)
        - Formato de registo – CEF
        - Tipos de registos - todos disponíveis

    1. Menos **de 3. Validar a ligação** - Verifique a ingestão de dados copiando o comando na página do conector e executando-o no seu reencaminhador de registo. Ver [PASSO 3: Validar a conectividade](connect-cef-verify.md) na documentação do Sentinela Azure para obter instruções e explicações mais detalhadas.

        Pode demorar até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **Azure Sentinel,** na tabela *CommonSecurityLog.*

Para consultar os dados do Servidor Secreto de Timótico no Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à medida que escolher:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Consulte o separador **passos seguintes** na página do conector para obter alguns livros úteis e amostras de consulta.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Servidor Secreto Thycóctico ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.