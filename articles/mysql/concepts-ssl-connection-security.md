---
title: Conectividade SSL - Base de Dados Azure para MySQL
description: Informações para configurar base de dados Azure para MySQL e aplicações associadas para utilizar corretamente ligações SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474276"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL na Base de Dados Azure para MySQL

A Base de Dados Azure para MySQL suporta ligar o seu servidor de base de dados a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="ssl-default-settings"></a>Definições predefinidas SSL

Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar-se ao MySQL.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao fornecer uma nova Base de Dados Azure para o servidor MySQL através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão. 

As cordas de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cordas de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. Sob a rubrica **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver a aplicação, consulte como configurar o [SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passos seguintes

[Bibliotecas de ligação para base de dados Azure para MySQL](concepts-connection-libraries.md)
