---
title: Conectividade SSL-banco de dados do Azure para MySQL
description: Informações para configurar o banco de dados do Azure para MySQL e os aplicativos associados para usar corretamente as conexões SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d677e7c80d98b15a638b00c5b8f4d390a492c7fa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770820"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL no banco de dados do Azure para MySQL
O banco de dados do Azure para MySQL dá suporte à conexão do servidor de banco de dados com aplicativos cliente usando protocolo SSL (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>Configurações padrão
Por padrão, o serviço de banco de dados deve ser configurado para exigir conexões SSL ao se conectar ao MySQL.  É recomendável evitar desabilitar a opção SSL sempre que possível. 

Ao provisionar um novo servidor de banco de dados do Azure para MySQL por meio do portal do Azure e da CLI, a imposição de conexões SSL é habilitada por padrão. 

As cadeias de conexão para várias linguagens de programação são mostradas na portal do Azure. Essas cadeias de conexão incluem os parâmetros SSL necessários para se conectar ao banco de dados. No portal do Azure, selecione o servidor. No título **configurações** , selecione as **cadeias de conexão**. O parâmetro SSL varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

Para saber como habilitar ou desabilitar a conexão SSL ao desenvolver o aplicativo, consulte [como configurar o SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Passos seguintes
[Bibliotecas de conexão para o banco de dados do Azure para MySQL](concepts-connection-libraries.md)
