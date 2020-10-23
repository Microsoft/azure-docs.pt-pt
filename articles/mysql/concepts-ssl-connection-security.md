---
title: Conectividade SSL/TLS - Base de Dados Azure para MySQL
description: Informações para configurar a Base de Dados Azure para o MySQL e aplicações associadas para utilizar corretamente as ligações SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 2969c963b491e4b08a0959d548e43ba11276d28a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126554"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Conectividade SSL/TLS na Base de Dados Azure para o MySQL

A Azure Database for MySQL suporta ligar o seu servidor de base de dados a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A imposição de ligações SSL entre o servidor de base de dados e as aplicações de cliente ajuda a proteger contra ataques "man-in-the-middle" ao encriptar o fluxo de dados entre o servidor e a sua aplicação.

> [!NOTE]
> A atualização do valor do parâmetro do `require_secure_transport` servidor não afeta o comportamento do serviço MySQL. Utilize as funcionalidades de execução SSL e TLS descritas neste artigo para garantir ligações à sua base de dados.

>[!NOTE]
> Com base no feedback dos clientes, alargamos a depreciação do certificado de raiz para a nossa CA raiz de Baltimore até 15 de fevereiro de 2021 (02/15/2021).

> [!IMPORTANT] 
> O certificado de raiz SSL está previsto para expirar a partir de 15 de fevereiro de 2021 (02/15/2021). Por favor, atualize a sua aplicação para utilizar o [novo certificado.](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) Para saber mais, consulte [as atualizações de certificados planeadas](concepts-certificate-rotation.md)

## <a name="ssl-default-settings"></a>Definições padrão SSL

Por predefinição, o serviço de base de dados deve ser configurado para exigir ligações SSL ao ligar ao MySQL.  Recomendamos que evite desativar a opção SSL sempre que possível.

Ao providenciar uma nova Base de Dados Azure para o servidor MySQL através do portal Azure e CLI, a aplicação das ligações SSL é ativada por padrão. 

As cadeias de ligação para várias linguagens de programação são mostradas no portal Azure. Essas cadeias de ligação incluem os parâmetros SSL necessários para ligar à sua base de dados. No portal Azure, selecione o seu servidor. No título **Definições,** selecione as **cordas de ligação**. O parâmetro SSL varia em base no conector, por exemplo "ssl=true" ou "sslmode=requir" ou "sslmode=required" e outras variações.

Em alguns casos, os pedidos requerem um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Atualmente, os clientes só podem **utilizar** o certificado predefinido para se ligarem a uma Base de Dados Azure para o servidor MySQL que está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Da mesma forma, as seguintes ligações apontam para os certificados para servidores em nuvens soberanas: [Governo azul,](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Alemanha](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

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
> Por predefinição, a Base de Dados Azure para o MySQL não impõe uma versão TLS mínima (a definição `TLSEnforcementDisabled` ).
>
> Uma vez executada uma versão TLS mínima, não pode desativar a aplicação mínima da versão.

Para saber como definir a definição de TLS para a sua Base de Dados Azure para o MySQL, consulte como [configurar a definição de TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Suporte cifra por Azure Database para mySQL Single servidor

Como parte da comunicação SSL/TLS, as suítes cifra são validadas e apenas os fatos de cifra de suporte são autorizados a comunicar ao serer da base de dados. A validação da suíte cifra é controlada na camada de [gateway](concepts-connectivity-architecture.md#connectivity-architecture) e não explicitamente no próprio nó. Se as suítes de cifra não corresponderem a uma das suites listadas abaixo, as ligações ao cliente recebidas serão rejeitadas.

### <a name="cipher-suite-supported"></a>Suíte cifra apoiada

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de conexão para Azure Database para MySQL](concepts-connection-libraries.md)
- Saiba como configurar o [SSL](howto-configure-ssl.md)
- Saiba como [configurar o TLS](howto-tls-configurations.md)
