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
ms.openlocfilehash: b82cf957f4bd74cb2c63bfd5a7fe73899b395df6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795819"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do Azure Marketplace

Sua imagem deve atender a essas recomendações de configuração de segurança. Isso ajuda a manter um alto nível de segurança para imagens de solução de parceiro no Azure Marketplace.

Sempre execute uma detecção de vulnerabilidade de segurança em sua imagem antes de enviá-la. Se você detectar uma vulnerabilidade de segurança em sua própria imagem publicada, deverá informar aos clientes em tempo hábil, tanto da vulnerabilidade quanto de como corrigi-la.

## <a name="open-source-based-images"></a>Imagens baseadas em código aberto

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificação**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Instale todos os patches de segurança mais recentes para a distribuição do Linux.                                                                                                                                                                                                              |
| Segurança                                                     | Siga as diretrizes do setor para proteger a imagem da VM para a distribuição específica do Linux.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo o mínimo de espaço com as funções, os recursos, os serviços e as portas de rede necessários do Windows Server.                                                                                                                                               |
| Segurança                                                     | Verificar o código-fonte e a imagem de VM resultante para malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas as contas bloqueadas necessárias que não têm senhas padrão que permitiriam logon interativo; sem back doors.                                                                                                                                           |
| Segurança                                                     | Desabilite as regras de firewall, a menos que o aplicativo dependa delas, como um dispositivo de firewall.                                                                                                                                                                             |
| Segurança                                                     | Remova todas as informações confidenciais da imagem do VHD, como chaves SSH de teste, arquivo de hosts conhecidos, arquivos de log e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | Evite usar o LVM.                                                                                                                                                                                                                                            |
| Segurança                                                     | Inclua as versões mais recentes das bibliotecas necessárias: </br> -OpenSSL v 1.0 ou superior </br> -Python 2,5 ou superior (Python 2.6 + é altamente recomendado) </br> -Pacote pyasn1 do Python se ainda não estiver instalado </br> -d. OpenSSL v 1,0 ou superior                                                                |
| Segurança                                                     | Limpar entradas de histórico de bash/shell.                                                                                                                                                                                                                                             |
| Redes                                                   | Inclua o servidor SSH por padrão. Defina SSH Keep Alive para sshd config com a seguinte opção: ClientAliveInterval 180.                                                                                                                                                        |
| Redes                                                   | Remova qualquer configuração de rede personalizada da imagem. Exclua a resolução de. conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Implementação                                                   | Instale o agente Linux do Azure mais recente.</br> -Instale usando o pacote RPM ou Deb.  </br> -Você também pode usar o processo de instalação manual, mas os pacotes do instalador são recomendados e preferenciais. </br> -Se estiver instalando o agente manualmente no repositório GitHub, primeiro copie o arquivo `waagent` para `/usr/sbin` e execute (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O arquivo de configuração do agente é colocado em `/etc/waagent.conf`. |
| Implementação                                                   | Verifique se o suporte do Azure pode fornecer aos nossos parceiros a saída do console serial quando necessário e fornecer tempo limite adequado para a montagem de disco do sistema operacional do armazenamento em nuvem. Adicione os seguintes parâmetros à linha de inicialização do kernel de imagem: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Implementação                                                   | Nenhuma partição de permuta no disco do sistema operacional. A permuta pode ser solicitada para a criação no disco de recursos local pelo agente do Linux.         |
| Implementação                                                   | Crie uma partição de raiz única para o disco do SO.      |
| Implementação                                                   | somente sistema operacional de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas no Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificação**                                                                                                                                                                |
| Segurança                                                         | Use uma imagem base segura do sistema operacional. O VHD usado para a origem de qualquer imagem baseada no Windows Server deve ser das imagens do sistema operacional do Windows Server fornecidas por meio de Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recentes.                                                                                                                                     |
| Segurança                                                         | Os aplicativos não devem depender de nomes de usuário restritos como administrador, raiz ou administrador.                                                                |
| Segurança                                                         | Habilite Criptografia de Unidade de Disco BitLocker para discos rígidos de so e discos rígidos de dados.                                                             |
| Segurança                                                         | Limite a superfície de ataque mantendo o mínimo de espaço com as funções, os recursos, os serviços e as portas de rede necessários do Windows Server habilitadas.                         |
| Segurança                                                         | Verificar o código-fonte e a imagem de VM resultante para malware.                                                                                                                     |
| Segurança                                                         | Defina atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas as contas bloqueadas necessárias que não têm senhas padrão que permitiriam logon interativo; sem back doors.                             |
| Segurança                                                         | Desabilite as regras de firewall, a menos que o aplicativo dependa delas, como um dispositivo de firewall.                                                               |
| Segurança                                                         | Remova todas as informações confidenciais da imagem do VHD, incluindo arquivos de HOSTs, arquivos de log e certificados desnecessários.                                              |
| Implementação                                                       | somente sistema operacional de 64 bits.                            |

Mesmo que sua organização não tenha imagens no Azure Marketplace, considere verificar as configurações de imagem do Windows e do Linux em relação a essas recomendações.

## <a name="contacting-customers"></a>Contatando clientes

Para identificar os clientes e seus emails de contato:

1.  Em Portal do Cloud Partner, no trilho esquerdo, selecione **insights**.
2.  Na guia **pedidos e uso** , use os campos **data de início** e **data de término** para o uso da consulta no intervalo de datas necessário. Isso mostra quais assinaturas do Azure foram usadas para a oferta diariamente. Exporte esses dados. 
3.  Da mesma forma, na guia **cliente** , consulte e exporte sua base de clientes.
4.  Corresponda a ID da assinatura da etapa 2 à ID da assinatura da etapa 3 para encontrar as informações necessárias do cliente.
