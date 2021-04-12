---
title: Problemas de conexão resolução de problemas - Azure Database for MySQL - Servidor Flexível
description: Saiba como resolver problemas de ligação à Base de Dados Azure para o MySQL Flexible Server.
keywords: conexão mysql,cadeia de ligação,problemas de conectividade,erro persistente,erro de ligação
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: 6fc8e08757ee067e0616cc701a3037a9dbf4c6fd
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491751"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Problemas de conexão de resolução de problemas para Azure Database para MySQL - Servidor Flexível

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Os problemas de ligação podem ser causados por uma variedade de coisas, incluindo:

* Definições de firewall
* Tempo de intervalo de ligação
* Informações incorretas de login
* Limite máximo atingido em algumas bases de dados Azure para recursos do Servidor Flexível MySQL

Neste artigo, discutiremos como pode resolver alguns dos erros e passos comuns para resolver estes erros.

## <a name="troubleshoot-common-errors"></a>Resolver erros comuns

Se a aplicação não ligar persistentemente à Base de Dados Azure para o MySQL Flexible Server, geralmente indica um problema com um dos seguintes:

* Ligação encriptada utilizando TLS/SSL: O Servidor Flexível suporta ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2) e todas as **ligações de entrada com TLS 1.0 e TLS 1.1 serão negadas por padrão**. Pode desativar a aplicação de ligações encriptadas ou alterar a versão TLS. Saiba mais sobre [conectividade encriptada utilizando a Segurança da Camada de Transporte (TLS 1.2) na Base de Dados Azure para o MySQL - Servidor Flexível](./how-to-connect-tls-ssl.md).
- Servidor Flexível em *acesso privado (Integração VNet)*: Certifique-se de que está a ligar-se a partir da mesma rede virtual que o servidor flexível. Consulte [rede virtual em Azure Database for MySQL Flexible Server]<!--(./concepts-networking-virtual-network.md)-->
- Servidor Flexível com *acesso público (endereços IP autorizados)*, certifique-se de que a firewall está configurada para permitir ligações do seu cliente. Consulte para [criar e gerir regras flexíveis de firewall do servidor utilizando o portal Azure](./how-to-manage-firewall-portal.md).
* Configuração da firewall do cliente: A firewall do seu cliente deve permitir ligações ao servidor de base de dados. Endereços IP e portas do servidor que não pode ser permitido, bem como nomes de aplicações como o MySQL em algumas firewalls.
* Erro do utilizador: Pode ter parâmetros de ligação mal definidos, como o nome do servidor na cadeia de ligação.

### <a name="resolve-connectivity-issues"></a>Resolver problemas de conectividade

* Consulte a [conectividade encriptada utilizando a Segurança da Camada de Transporte (TLS 1.2) na Base de Dados Azure para o MySQL - Servidor Flexível](./how-to-connect-tls-ssl.md) --> para saber mais sobre ligações encriptadas.
* Se estiver a utilizar **o acesso público (endereços IP autorizados)**, então crie [regras de firewall](./how-to-manage-firewall-portal.md) para permitir o endereço IP do cliente. Apenas para testes temporários, crie uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando o endereço IP inicial e utilizando o endereço IP inicial 255.255.255.255 como o endereço IP final. Isto abrirá o servidor a todos os endereços IP. Se isto resolver o seu problema de conectividade, remova esta regra e crie uma regra de firewall para um endereço IP ou intervalo de endereços devidamente limitado.
* Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 3306 está aberta para ligações de saída.
* Verifique a sua cadeia de ligação e outras definições de ligação. Consulte as cadeias de conexão pré-definidas na página **'Connection Strings'** disponível para o seu servidor no portal Azure para línguas comuns.

## <a name="next-steps"></a>Passos seguintes
- Utilize a bancada [mySQL workbench para ligar e consultar dados na Base de Dados Azure para o MySQL Flexible Server](./connect-workbench.md).
- [Utilize PHP para ligar e consultar dados na Base de Dados Azure para o MySQL Flexible Server](./connect-php.md).
- [Utilize python para ligar e consultar dados na Base de Dados Azure para o MySQL Flexible Server](./connect-python.md).
