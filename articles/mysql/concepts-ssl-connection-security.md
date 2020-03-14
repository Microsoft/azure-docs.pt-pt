---
title: Conectividade SSL - Base de Dados Azure para MySQL
description: Informações para configurar base de dados Azure para MySQL e aplicações associadas para utilizar corretamente ligações SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7bc3a238ca2ff2861ec6fc65033738e741574321
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370853"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL na Base de Dados Azure para MySQL

A Base de Dados Azure para MySQL suporta ligar o seu servidor de base de dados a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="ssl-default-settings"></a>Definições predefinidas SSL

Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar-se ao MySQL.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao fornecer uma nova Base de Dados Azure para o servidor MySQL através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão. 

As cordas de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cordas de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. Sob a rubrica **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em função do conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver a aplicação, consulte como configurar o [SSL](howto-configure-ssl.md).

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>Conectividade TLS na Base de Dados Azure para MySQL

A Base de Dados Azure para MySQL suporta encriptação para clientes que se conectam ao seu servidor de base de dados utilizando a Transport Layer Security (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede seguras entre o seu servidor de base de dados e aplicações de clientes, permitindo-lhe cumprir os requisitos de conformidade.

### <a name="tls-settings"></a>Definições tls

Os clientes têm agora a capacidade de impor a versão TLS para o cliente que se conecta à sua Base de Dados Azure para o MySQL. Para utilizar a opção TLS, utilize a definição mínima de opções de **versão TLS.** São permitidos os seguintes valores para esta definição de opção:

|  Definição mínima de TLS             | Versão TLS suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário tLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior          |
| TLS1_2                           | Versão TLS 1.2 e superior           |


Por exemplo, definir esta versão de definição TLS mínima para TLS 1.0 significa que o seu servidor permitirá ligações de clientes utilizando TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> A Base de Dados Azure para falhas mySQL para TLS sendo desativada para todos os novos servidores.
>
> Atualmente, as versões TLS suportadas pela Base de Dados Azure para MySQL são TLS 1.0, 1.1 e 1.2.

Para saber como definir a definição de TLS para a sua Base de Dados Azure para MySQL, consulte [como configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Passos seguintes

[Bibliotecas de ligação para base de dados Azure para MySQL](concepts-connection-libraries.md)
