---
title: Conectividade SSL/TLS - Base de Dados Azure para MySQL
description: Informações para configurar a Base de Dados Azure para o MySQL e aplicações associadas para utilizar corretamente as ligações SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 2421f8a9396b47d04db35a7cad843f6baa6f6177
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416108"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL/TLS na Base de Dados Azure para o MySQL

A Azure Database for MySQL suporta ligar o seu servidor de base de dados a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

## <a name="ssl-default-settings"></a>Definições padrão SSL

Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar ao MySQL.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao providenciar uma nova Base de Dados Azure para o servidor MySQL através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão. 

As cadeias de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cadeias de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. No título **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em base no conector, por exemplo "ssl=true" ou "sslmode=requir" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver uma aplicação, consulte como [configurar o SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Aplicação do TLS na Base de Dados Azure para o MySQL

A Azure Database for MySQL suporta encriptação para clientes que se conectam ao seu servidor de base de dados usando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações seguras de rede entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

### <a name="tls-settings"></a>Definições de TLS

A Azure Database for MySQL fornece a capacidade de impor a versão TLS para as ligações do cliente. Para fazer cumprir a versão TLS, utilize a definição de option de **versão TLS mínima.** São permitidos os seguintes valores para a definição desta opção:

|  Definição mínima de TLS             | Versão TLS do Cliente suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário nenhum TLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior           |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior                   |
| TLS1_2                           | Versão TLS 1.2 e superior                     |


Por exemplo, definir o valor da versão mínima de definição de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes que utilizem TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2+ e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> Azure Database for MySQL predefinições para TLS ser desativado para todos os novos servidores.
>
> Atualmente, as versões TLS suportadas pela Azure Database para o MySQL são TLS 1.0, 1.1 e 1.2. Uma vez aplicada a uma versão TLS mínima específica, não pode alterá-la para desativada.

Para saber como definir a definição de TLS para a sua Base de Dados Azure para o MySQL, consulte como [configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de conexão para Azure Database para MySQL](concepts-connection-libraries.md)
- Saiba como configurar o [SSL](howto-configure-ssl.md)
- Saiba como [configurar o TLS](howto-tls-configurations.md)
