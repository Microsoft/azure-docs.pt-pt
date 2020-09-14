---
title: Conectividade SSL/TLS - Base de Dados Azure para MariaDB
description: Informações para configurar a Base de Dados de Azure para o MariaDB e aplicações associadas para utilizar corretamente as ligações SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: a108459985f235f0280354ef7b4fa0cb181f5dda
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054250"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Conectividade SSL/TLS na Base de Dados Azure para MariaDB
A Azure Database for MariaDB suporta ligar o seu servidor de base de dados a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

> [!IMPORTANT] 
> O certificado de raiz SSL está previsto expirar a partir de 26 de outubro de 2020 (10/26/2020). Por favor, atualize a sua aplicação para utilizar o [novo certificado.](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) Para saber mais, consulte [as atualizações de certificados planeadas](concepts-certificate-rotation.md)

## <a name="default-settings"></a>Predefinições
Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar-se ao MariaDB.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao providenciar uma nova Base de Dados Azure para o servidor MariaDB através do portal Azure e do CLI, a aplicação das ligações SSL é ativada por padrão.

Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Atualmente, os clientes só podem **utilizar** o certificado predefinido para ligar a uma Base de Dados Azure para o servidor MariaDB que está localizada em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Da mesma forma, as seguintes ligações apontam para os certificados para servidores em nuvens soberanas: [Governo azul,](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Alemanha](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

As cadeias de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cadeias de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. No título **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em base no conector, por exemplo "ssl=true" ou "sslmode=requir" ou "sslmode=required" e outras variações.

Para saber como ativar ou desativar a ligação SSL ao desenvolver uma aplicação, consulte como [configurar o SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Aplicação da TLS na Base de Dados Azure para MariaDB

A Azure Database for MariaDB suporta encriptação para clientes que se conectam ao seu servidor de base de dados usando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações seguras de rede entre o servidor da base de dados e as aplicações do cliente, permitindo-lhe aderir aos requisitos de conformidade.

### <a name="tls-settings"></a>Definições de TLS

A Azure Database for MariaDB fornece a capacidade de impor a versão TLS para as ligações ao cliente. Para fazer cumprir a versão TLS, utilize a definição de option de **versão TLS mínima.** São permitidos os seguintes valores para a definição desta opção:

|  Definição mínima de TLS             | Versão TLS do Cliente suportada                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário nenhum TLS                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 e superior         |
| TLS1_1                           | TLS 1.1, TLS 1.2 e superior              |
| TLS1_2                           | Versão TLS 1.2 e superior                  |


Por exemplo, definir o valor da versão de definição mínima de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes que utilizem TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2+ e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

> [!Note] 
> Por predefinição, a Base de Dados Azure para MariaDB não impõe uma versão TLS mínima (a definição `TLSEnforcementDisabled` ).
>
> Uma vez executada uma versão TLS mínima, não pode desativar a aplicação mínima da versão.

Para aprender a configurar a definição de TLS para a sua Base de Dados Azure para MariaDB, consulte como [configurar a definição de TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [as regras de firewall do servidor](concepts-firewall-rules.md)
- Saiba como configurar o [SSL](howto-configure-ssl.md)
- Saiba como [configurar o TLS](howto-tls-configurations.md)
