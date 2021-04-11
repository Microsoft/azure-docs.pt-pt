---
title: Começar com tmaxSoft OpenFrame em Azure Virtual Machines
description: Reenquadrem as suas cargas de trabalho de computador principal IBM z/OS utilizando o ambiente TmaxSoft OpenFrame em Máquinas Virtuais Azure (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950676"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Começar com tmaxSoft OpenFrame em Azure

Pegue nos seus ativos de computador principal existentes e mova-os para o Microsoft Azure usando o TmaxSoft OpenFrame. Esta popular solução de rehostação cria um ambiente de emulação em Azure, permitindo-lhe migrar rapidamente aplicações. Não é necessário reformar.

## <a name="openframe-rehosting-environment"></a>Ambiente de rehosting OpenFrame

Crie um ambiente OpenFrame em Azure para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Como mostra a seguinte figura, o OpenFrame inclui vários componentes que criam o ambiente de emulação do computador principal em Azure. Por exemplo, os serviços online OpenFrame substituem o middleware do computador principal, como o IBM Customer Information Control System (CICS). O OpenFrame Batch, com o seu componente TJES, substitui o Subsistema de Entrada de Emprego (JES) do computador principal da IBM. 

![Processo de rehosting OpenFrame](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente OpenFrame em Azure, você deve ter uma licença de produto válida ou licença de teste da TmaxSoft.

## <a name="openframe-components"></a>Componentes OpenFrame

Os seguintes componentes fazem parte do ambiente OpenFrame em Azure:

- **Ferramentas de migração,** incluindo OFMiner, uma solução que analisa os ativos dos mainframes e depois os migra para Azure.
- **Compiladores**, incluindo OFCOBOL, um compilador que interpreta os programas COBOL do mainframe; OFPLI, que interpreta os programas pl/I do computador principal; e OFASM, um compilador que interpreta os programas de montagem do mainframe.
- **Componentes frontais,** incluindo Java Enterprise User Solution (JEUS), um servidor de aplicação web certificado para Java Enterprise Edition 6.OFGW, e o componente OpenFrame gateway que fornece um ouvinte de 3270.
- **Ambiente de aplicação.** OpenFrame Base é o middleware que gere todo o sistema. OpenFrame Server Type C (OSC) substitui o middleware do computador principal e o CICS IBM.
- **Base de dados relacional**, como Tibero (mostrado), Oracle Database, Microsoft SQL Server, IBM Db2 ou MySQL. As aplicações OpenFrame utilizam o protocolo Open Database Connectivity (ODBC) para comunicar com a base de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso do utilizador a sistemas e recursos. 
- **OfManager** é uma solução que fornece funções de funcionamento e gestão do OpenFrame no ambiente web.

![Arquitetura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passos seguintes

- [Instalar o TmaxSoft OpenFrame no Azure](./install-openframe-azure.md)
