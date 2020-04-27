---
title: Começar com TmaxSoft OpenFrame em Máquinas Virtuais Azure
description: Realoja as suas cargas de trabalho do mainframe IBM z/OS utilizando o ambiente OpenFrame TmaxSoft em Máquinas Virtuais Azure (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61485546"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Começa com o OpenFrame TmaxSoft no Azure

Pegue nos ativos principais existentes e mude-os para o Microsoft Azure utilizando o TmaxSoft OpenFrame. Esta solução popular de realojamento cria um ambiente de emulação em Azure, permitindo-lhe migrar rapidamente aplicações. Não é necessário reformar.

## <a name="openframe-rehosting-environment"></a>Ambiente de realojamento OpenFrame

Criar um ambiente OpenFrame no Azure para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Como mostra a figura seguinte, o OpenFrame inclui múltiplos componentes que criam o ambiente de emulação do computador principal no Azure. Por exemplo, os serviços online OpenFrame substituem o middleware mainframe, como o IBM Customer Information Control System (CICS). O OpenFrame Batch, com o seu componente TJES, substitui o subsistema de entrada de emprego (JES) do mainframe IBM. 

![Processo de realojamento openFrame](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente OpenFrame no Azure, você deve ter uma licença de produto ou licença de teste válida da TmaxSoft.

## <a name="openframe-components"></a>Componentes OpenFrame

Os seguintes componentes fazem parte do ambiente OpenFrame no Azure:

- **Ferramentas** de migração incluindo a OFMiner, uma solução que analisa os ativos principais e depois os migra para o Azure.
- **Compiladores**, incluindo ofcobol, um compilador que interpreta os programas COBOL do mainframe; OFPLI, que interpreta os programas PL/I do mainframe; e OFASM, um compilador que interpreta os programas de montagem do computador principal.
- **Componentes frontais,** incluindo a Java Enterprise User Solution (JEUS), um servidor de aplicação web certificado para java Enterprise Edition 6.OFGW, e o componente de gateway OpenFrame que fornece um ouvinte 3270.
- Ambiente de **aplicação.** OpenFrame Base é o middleware que gere todo o sistema. O OpenFrame Server Type C (OSC) substitui o middleware do mainframe e o CICS IBM.
- **Base de dados relacional**, como Tibero (mostrado), Oracle Database, Microsoft SQL Server, IBM Db2 ou MySQL. As aplicações OpenFrame utilizam o protocolo de conectividade de base de dados aberta (ODBC) para comunicar com a base de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso do utilizador a sistemas e recursos. 
- **OfManager** é uma solução que fornece as funções de funcionamento e gestão do OpenFrame no ambiente web.

![Arquitetura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passos seguintes

- [Instale o OpenFrame TmaxSoft no Azure](./install-openframe-azure.md)
