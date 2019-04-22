---
title: Introdução ao TmaxSoft OpenFrame em máquinas de virtuais do Azure
description: Realojar IBM z/OS mainframe cargas de trabalho usando o ambiente de TmaxSoft OpenFrame em máquinas virtuais do Azure (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896472"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Introdução ao TmaxSoft OpenFrame no Azure

Usar os recursos de mainframe existentes e movê-los para o Microsoft Azure utilizando TmaxSoft OpenFrame. Esta solução rehosting popular cria um ambiente de emulação no Azure, permitindo-lhe migrar rapidamente as aplicações. Não existem reformatação é necessário.

## <a name="openframe-rehosting-environment"></a>Ambiente de realojamento OpenFrame

Configure um ambiente de OpenFrame no Azure para desenvolvimento, demonstrações, teste ou cargas de trabalho de produção. Como mostra a figura a seguir, OpenFrame inclui vários componentes que criar o ambiente de emulação de mainframe no Azure. Por exemplo, os serviços online do OpenFrame substituem o middleware de mainframe, como o IBM Customer informações controle sistema (CICS). OpenFrame Batch, com seu componente TJES, substitui o subsistema de entrada de tarefa de (JES do mainframe IBM). 

![Processo de realojamento OpenFrame](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente de OpenFrame no Azure, tem de ter uma licença de produto válida ou uma licença de avaliação do TmaxSoft.

## <a name="openframe-components"></a>Componentes de OpenFrame

Os seguintes componentes fazem parte do ambiente OpenFrame no Azure:

- **Ferramentas de migração** incluindo OFMiner, uma solução que analisa os ativos de mainframes e os migra para o Azure.
- **Compiladores**, incluindo OFCOBOL, um compilador que interpreta os programas de COBOL do mainframe; OFPLI, que interpreta o mainframe PL / eu programas; e OFASM, um compilador que interpreta os programas de montador o mainframe.
- **Front-end** componentes, incluindo Java Enterprise utilizador solução (JEUS), um servidor de aplicação web que está certificado para 6.OFGW Java Enterprise Edition e o componente de gateway OpenFrame que fornece um serviço de escuta 3270.
- **Aplicação** ambiente. OpenFrame Base é o middleware que gerencia todo o sistema. C de tipo de servidor de OpenFrame (OSC) substitui o mainframe middleware e IBM CICS.
- **Base de dados relacional**, como Tibero (mostrado), Oracle Database, Microsoft SQL Server, IBM Db2 ou MySQL. As aplicações de OpenFrame utilizam o protocolo de conectividade ODBC (Open Database) para comunicar com a base de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso de utilizador para sistemas e recursos. 
- **OFManager** é uma solução que fornece funções de gerenciamento e operação do OpenFrame no ambiente de web.

![Arquitetura de OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Passos Seguintes

- [Instalar o TmaxSoft OpenFrame no Azure](./install-openframe-azure.md)
