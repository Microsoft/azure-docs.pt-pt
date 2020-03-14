---
title: Conectividade SSL - Base de Dados Azure para MariaDB
description: Informações para configurar base de dados Azure para MariaDB e aplicações associadas para utilizar corretamente ligações SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c03a56176a6e2cc995e74017b60747541fc843bb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371635"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Conectividade SSL na Base de Dados Azure para MariaDB
A Base de Dados Azure para O MariaDB suporta a ligação do seu servidor de base de dados às aplicações do cliente utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="default-settings"></a>Definições predefinidas
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar-se ao MariaDB.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao fornecer uma nova Base de Dados Azure para o servidor MariaDB através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão.

As cordas de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cordas de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. Sob a rubrica **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver a aplicação, consulte como configurar o [SSL](howto-configure-ssl.md).

## <a name="tls-connectivity-in-azure-database-for-mariadb"></a>Conectividade TLS na Base de Dados Azure para MariaDB

A Base de Dados Azure para MariaDB suporta encriptação para clientes que se conectam ao seu servidor de base de dados utilizando a Transport Layer Security (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede seguras entre o seu servidor de base de dados e aplicações de clientes, permitindo-lhe cumprir os requisitos de conformidade.

### <a name="tls-settings"></a>Definições tls

A Base de Dados Azure para a MariaDB fornece a capacidade de impor a versão TLS para as ligações do cliente. Para utilizar a opção TLS, utilize a definição mínima de opções de **versão TLS.** São permitidos os seguintes valores para esta definição de opção:

|  Definição mínima de TLS             | Versão TLS suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário tLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior          |
| TLS1_2                           | Versão TLS 1.2 e superior           |


Por exemplo, definir esta versão de definição Mínima de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes utilizando TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> A Base de Dados Azure para as falhas do MariaDB no TLS está a ser desativada para todos os novos servidores.
>
> Atualmente as versões TLS suportadas pela Base de Dados Azure para MariaDB são TLS 1.0, 1.1 e 1.2.

Para saber como definir a definição de TLS para a sua Base de Dados Azure para MariaDB, consulte [como configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as regras de firewall do servidor](concepts-firewall-rules.md)
- Saiba como [configurar o SSL](howto-configure-ssl.md).
- Saiba como [configurar o TLS](howto-tls-configurations.md).