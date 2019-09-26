---
title: Recomendações de segurança para imagens do Azure Marketplace | Microsoft Docs
description: Este artigo fornece recomendações para imagens incluídas no mercado
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: 3d6b1ed2a3800058acba9fe5df2137811afed74a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300776"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do Azure Marketplace

Recomendamos que cada solução esteja em conformidade com as seguintes recomendações de configuração de segurança. Isso ajuda a manter um alto nível de segurança para imagens de solução de parceiro no Azure Marketplace.

Essas recomendações também podem ser úteis para organizações que não têm imagens no Azure Marketplace. Talvez você queira verificar as configurações de imagem do Windows e Linux da sua empresa em relação às diretrizes encontradas nas seguintes tabelas:

## <a name="open-source-based-images"></a>Imagens baseadas em código aberto

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificação**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Todos os patches de segurança mais recentes para a distribuição do Linux são instalados.                                                                                                                                                                                                              |
| Segurança                                                     | As diretrizes do setor para proteger a imagem da VM para a distribuição do Linux específica foram seguidas.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo o mínimo de espaço com as funções, os recursos, os serviços e as portas de rede necessários do Windows Server.                                                                                                                                               |
| Segurança                                                     | Verificar o código-fonte e a imagem de VM resultante para malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas as contas bloqueadas necessárias, que não têm senhas padrão que permitiriam logon interativo; sem back doors.                                                                                                                                           |
| Segurança                                                     | As regras de firewall são desabilitadas, a menos que a funcionalidade do aplicativo dependa delas, como um dispositivo de firewall.                                                                                                                                                                             |
| Segurança                                                     | Todas as informações confidenciais foram removidas da imagem do VHD, como chaves SSH de teste, arquivo de hosts conhecidos, arquivos de log e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | É recomendável que o LVM não deva ser usado.                                                                                                                                                                                                                                            |
| Segurança                                                     | As versões mais recentes das bibliotecas necessárias devem ser incluídas: </br> -OpenSSL v 1.0 ou superior </br> -Python 2,5 ou superior (Python 2.6 + é altamente recomendado) </br> -Pacote pyasn1 do Python se ainda não estiver instalado </br> -d. OpenSSL v 1,0 ou superior                                                                |
| Segurança                                                     | Entradas de histórico de bash/shell devem ser limpas                                                                                                                                                                                                                                             |
| Redes                                                   | O servidor SSH deve ser incluído por padrão. Defina SSH Keep Alive para sshd config com a seguinte opção: ClientAliveInterval 180                                                                                                                                                        |
| Redes                                                   | A imagem não deve conter nenhuma configuração de rede personalizada. Exclua o botão de resolução. conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Implementação                                                   | O agente Linux do Azure mais recente deve ser instalado </br> -O agente deve ser instalado usando o pacote RPM ou Deb.  </br> -Você também pode usar o processo de instalação manual, mas os pacotes do instalador são recomendados e preferenciais. </br> -Se estiver instalando o agente manualmente do repositório GitHub, primeiro copie `waagent` o arquivo `/usr/sbin` para e execute (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O arquivo de configuração do agente é `/etc/waagent.conf`colocado em.    |
| Implementação                                                   | Garante que o suporte do Azure possa fornecer aos nossos parceiros a saída do console serial quando necessário e fornecer tempo limite adequado para a montagem de disco do sistema operacional do armazenamento em nuvem. A imagem deve ter adicionado os seguintes parâmetros à linha de inicialização do kernel:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implementação                                                   | Nenhuma partição de permuta no disco do sistema operacional. A permuta pode ser solicitada para a criação no disco de recursos local pelo agente do Linux.         |
| Implementação                                                   | É recomendável que uma única partição raiz seja criada para o disco do sistema operacional.      |
| Implementação                                                   | somente sistema operacional de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas no Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificação**                                                                                                                                                                |
| Segurança                                                         | Use uma imagem base segura do sistema operacional. O VHD usado para a origem de qualquer imagem baseada no Windows Server deve ser das imagens do sistema operacional do Windows Server fornecidas por meio de Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recentes.                                                                                                                                     |
| Segurança                                                         | Os aplicativos não devem ter uma dependência de nomes de usuário restritos, como administrador, raiz e administrador.                                                                |
| Segurança                                                         | O Criptografia de Unidade de Disco BitLocker está habilitado para discos rígidos de so e discos rígidos de dados.                                                             |
| Segurança                                                         | Limite a superfície de ataque mantendo o mínimo de espaço com as funções, os recursos, os serviços e as portas de rede necessários do Windows Server habilitadas.                         |
| Segurança                                                         | Verificar o código-fonte e a imagem de VM resultante para malware.                                                                                                                     |
| Segurança                                                         | Defina atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas as contas bloqueadas necessárias, que não têm senhas padrão que permitiriam logon interativo; sem back doors.                             |
| Segurança                                                         | As regras de firewall são desabilitadas, a menos que a funcionalidade do aplicativo dependa delas, como um dispositivo de firewall.                                                               |
| Segurança                                                         | Todas as informações confidenciais foram removidas da imagem VHD. Por exemplo, arquivos de host, arquivos de log e certificados desnecessários devem ser removidos.                                              |
| Implementação                                                       | somente sistema operacional de 64 bits.                            |
