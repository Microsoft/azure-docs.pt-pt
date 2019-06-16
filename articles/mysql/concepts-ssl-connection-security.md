---
title: Conectividade SSL para a base de dados do Azure para MySQL
description: Informações para configurar a base de dados do Azure para MySQL e aplicações associadas a corretamente utilizar ligações SSL
author: JasonMAnderson
ms.author: janders
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 129f90d495627edb25dfafdeb1b274aa2c4c71cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525821"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL na base de dados do Azure para MySQL
Base de dados do Azure para MySQL suporta a ligar o seu servidor de base de dados para aplicações de cliente, utilizando Secure Sockets Layer (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>Predefinições
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar ao MySQL.  Recomendamos que evite a desativar a opção de SSL, sempre que possível. 

Quando aprovisionar uma nova base de dados do Azure para o servidor MySQL através do portal do Azure e a CLI, imposição de ligações SSL está ativada por predefinição. 

Cadeias de ligação para várias linguagens de programação são apresentadas no portal do Azure. Essas cadeias de caracteres de conexão incluem os parâmetros SSL necessários para ligar à base de dados. No portal do Azure, selecione o seu servidor. Sob o **definições** cabeçalho, selecione a **cadeias de ligação**. O parâmetro SSL varia com base no conector, por exemplo "ssl = true" ou "sslmode = exigir" ou "sslmode = necessária" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolvimento de aplicativo, consulte [como configurar o SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Passos Seguintes
[Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md)
