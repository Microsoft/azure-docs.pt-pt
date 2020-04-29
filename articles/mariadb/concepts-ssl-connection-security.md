---
title: Conectividade SSL - Base de Dados Azure para MariaDB
description: Informações para configurar base de dados Azure para MariaDB e aplicações associadas para utilizar corretamente ligações SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477073"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Conectividade SSL na Base de Dados Azure para MariaDB
A Base de Dados Azure para O MariaDB suporta a ligação do seu servidor de base de dados às aplicações do cliente utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>Predefinições
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar-se ao MariaDB.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao fornecer uma nova Base de Dados Azure para o servidor MariaDB através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão.

As cordas de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cordas de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. Sob a rubrica **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver a aplicação, consulte como configurar o [SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as regras de firewall do servidor](concepts-firewall-rules.md)
- Saiba como [configurar o SSL](howto-configure-ssl.md).
