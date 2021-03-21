---
title: Configurar configurações de cache Azure HPC
description: Explica como configurar configurações adicionais para a cache como MTU, configuração personalizada NTP e DNS, e como aceder às imagens expressas a partir de alvos de armazenamento Azure Blob.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 06feefe3a934d1ee02793fab442852e5ef40899a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563387"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurar configurações adicionais de cache Azure HPC

A página **de Networking** no portal Azure tem opções para personalizar várias configurações. A maioria dos utilizadores não precisa de alterar estas definições a partir dos seus valores predefinidos.

Este artigo também descreve como usar a funcionalidade de instantâneo para alvos de armazenamento Azure Blob. A função snapshot não tem configurações configuráveis.

Para ver as definições, abra a página de **rede** do cache no portal Azure.

![screenshot da página de networking no portal Azure](media/networking-page.png)

> [!NOTE]
> Uma versão anterior desta página incluía uma configuração de squash de raiz ao nível de cache, mas esta definição foi movida para [as políticas](access-policies.md)de acesso ao cliente .

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Ajuste o valor mtu
<!-- linked from troubleshoot-nas article -->

Pode selecionar o tamanho máximo da unidade de transmissão para a cache utilizando o tamanho **mtu** do menu suspenso.

O valor predefinido é de 1500 bytes, mas pode alterá-lo para 1400.

> [!NOTE]
> Se baixar o tamanho MTU da cache, certifique-se de que os clientes e os sistemas de armazenamento que comunicam com a cache têm a mesma definição DE MTU ou um valor mais baixo.

A redução do valor de cache MTU pode ajudá-lo a contornar as restrições de tamanho do pacote no resto da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de 1500 bytes com sucesso. Reduzir o tamanho dos pacotes enviados pela VPN pode eliminar esse problema. No entanto, note que uma configuração MTU de cache inferior significa que qualquer outro componente que comunique com a cache - incluindo clientes e sistemas de armazenamento - também deve ter uma definição de MTU mais baixa para evitar problemas de comunicação.

Se não pretender alterar as definições de MTU noutros componentes do sistema, não deverá baixar a definição de MTU da cache. Existem outras soluções para trabalhar em torno das restrições de tamanho de pacote VPN. Leia Ajustar as restrições de [tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) no artigo de resolução de problemas da NAS para saber mais sobre o diagnóstico e a resolução deste problema.

Saiba mais sobre as definições de MTU em redes virtuais Azure lendo [afinação de desempenho TCP/IP para VMs Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Personalizar NTP

O seu cache utiliza o servidor de tempo baseado em Azure time.microsoft.com por predefinição. Se quiser que o seu cache utilize um servidor NTP diferente, especifique-o na secção **de configuração NTP.** Utilize um nome de domínio totalmente qualificado ou um endereço IP.

## <a name="set-a-custom-dns-configuration"></a>Desa parte para uma configuração de DNS personalizada

> [!CAUTION]
> Não altere a configuração de DNS de cache se não precisar. Erros de configuração podem ter consequências terríveis. Se a sua configuração não conseguir resolver os nomes de serviçoS Azure, a instância de cache HPC tornar-se-á permanentemente inacessível.

A Azure HPC Cache é configurado automaticamente para utilizar o sistema Azure DNS seguro e conveniente. No entanto, algumas configurações incomuns requerem que a cache utilize um sistema DNS separado e no local em vez do sistema Azure. A secção de **configuração DNS** da página **de Networking** é utilizada para especificar este tipo de sistema.

Consulte os seus representantes do Azure ou consulte o Microsoft Service and Support para determinar se precisa ou não de utilizar uma configuração de DNS de cache personalizada.

Se configurar o seu próprio sistema DNS no local para a Cache Azure HPC utilizar, deve certificar-se de que a configuração pode resolver os nomes do ponto final do Azure para os serviços Azure. Tem de configurar o seu ambiente DNS personalizado para encaminhar determinados pedidos de resolução de nomes para o Azure DNS ou para outro servidor, conforme necessário.

Verifique se a sua configuração DNS pode resolver estes itens com sucesso antes de o utilizar para uma Cache Azure HPC:

* ``*.core.windows.net``
* Lista de revogação de certificados (CRL) serviços de verificação do protocolo de verificação do certificado online (OCSP). Uma lista parcial é fornecida no item das [regras](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) de firewall no final deste artigo do [Azure TLS](../security/fundamentals/tls-certificate-changes.md), mas deve consultar um representante técnico da Microsoft para compreender todos os requisitos.
* O nome de domínio totalmente qualificado do seu servidor NTP (time.microsoft.com ou um servidor personalizado)

Se precisar de configurar um servidor DNS personalizado para a sua cache, utilize os campos fornecidos:

* **Domínio de pesquisa de DNS** (opcional) - Introduza o seu domínio de pesquisa, por exemplo, ``contoso.com`` . Um único valor é permitido, ou pode deixá-lo em branco.
* **Servidor(s) DNS -** Introduza até três servidores DNS. Especifique-os por endereço IP.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

### <a name="refresh-storage-target-dns"></a>Atualizar alvo de armazenamento DNS

Se o servidor DNS atualizar endereços IP, os alvos de armazenamento NFS associados ficarão temporariamente indisponíveis. Leia como atualizar os endereços IP do seu sistema DNS personalizados em [editar alvos de armazenamento](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Ver instantâneos para alvos de armazenamento de bolhas

A Azure HPC Cache guarda automaticamente imagens de armazenamento para alvos de armazenamento Azure Blob. As imagens fornecem um ponto de referência rápido para o conteúdo do recipiente de armazenamento de fundo.

As imagens não substituem as cópias de segurança de dados e não incluem qualquer informação sobre o estado dos dados em cache.

> [!NOTE]
> Esta funcionalidade de instantâneo é diferente da funcionalidade snapshot incluída no netApp ou no software de armazenamento Isilon. Essas implementações instantâneas limpam as alterações da cache para o sistema de armazenamento de back-end antes de tirar a fotografia.
>
> Para eficiência, o instantâneo Azure HPC Cache não lava as alterações primeiro, e apenas regista dados que foram escritos no recipiente Blob. Este instantâneo não representa o estado dos dados em cache, pelo que pode não incluir alterações recentes.

Esta funcionalidade está disponível apenas para alvos de armazenamento Azure Blob, e a sua configuração não pode ser alterada.

As fotos são tiradas a cada oito horas, às 0:00, 08:00 e às 16:00.

Azure HPC Cache armazena fotos diárias, semanais e mensais até serem substituídas por novas. Os limites são:

* Até 20 instantâneos diários
* Até 8 instantâneos semanais
* Até 3 instantâneos mensais

Aceda às fotos do `.snapshot` diretório no espaço de nome do seu alvo de armazenamento de bolhas.
