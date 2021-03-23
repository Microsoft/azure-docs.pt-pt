---
title: Resolução de problemas Azure HPC Cache NFS alvos de armazenamento
description: Dicas para evitar e corrigir erros de configuração e outros problemas que podem causar falhas ao criar um alvo de armazenamento NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775702"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Resolução de problemas Na configuração e problemas de alvo de armazenamento NFS

Este artigo fornece soluções para alguns erros de configuração comuns e outros problemas que poderiam impedir a Azure HPC Cache de adicionar um sistema de armazenamento NFS como alvo de armazenamento.

Este artigo inclui detalhes sobre como verificar portas e como permitir o acesso à raiz a um sistema NAS. Inclui também informações detalhadas sobre questões menos comuns que podem levar à criação de alvos de armazenamento NFS.

> [!TIP]
> Antes de utilizar este guia, leia [os pré-requisitos para os alvos de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).

Se a solução para o seu problema não estiver incluída aqui, por favor [abra um bilhete de suporte](hpc-cache-support-ticket.md) para que o Microsoft Service and Support possa trabalhar consigo para investigar e resolver o problema.

## <a name="check-port-settings"></a>Verifique as definições da porta

A Azure HPC Cache precisa de acesso de leitura/escrita a várias portas UDP/TCP no sistema de armazenamento nas nas traseiras. Certifique-se de que estas portas estão acessíveis no sistema NAS e também que o tráfego é permitido a estas portas através de quaisquer firewalls entre o sistema de armazenamento e a sub-rede de cache. Poderá ser necessário trabalhar com porta-redes e firewall para o seu centro de dados para verificar esta configuração.

As portas são diferentes para sistemas de armazenamento de diferentes fornecedores, por isso verifique os requisitos do seu sistema ao configurar um alvo de armazenamento.

Em geral, a cache precisa de acesso a estas portas:

| Protocolo | Porta  | Serviço  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | montado   |
| TCP/UDP  | 4047  | status   |

Para conhecer as portas específicas necessárias para o seu sistema, utilize o seguinte ``rpcinfo`` comando. Este comando abaixo lista as portas e formatos os resultados relevantes numa tabela. (Utilize o endereço IP do seu sistema no lugar do *termo<storage_IP>.)*

Pode emitir este comando a qualquer cliente Linux que tenha infraestrutura NFS instalada. Se utilizar um cliente dentro da sub-rede de cluster, também pode ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Certifique-se de que todas as portas devolvidas pela ``rpcinfo`` consulta permitem o tráfego ilimitado da sub-rede da Cache Azure HPC.

Verifique estas definições tanto no próprio NAS como em quaisquer firewalls entre o sistema de armazenamento e a sub-rede de cache.

## <a name="check-root-access"></a>Verifique o acesso à raiz

A Azure HPC Cache precisa de acesso às exportações do seu sistema de armazenamento para criar o alvo de armazenamento. Especificamente, monta as exportações como ID 0 do utilizador.

Diferentes sistemas de armazenamento utilizam diferentes métodos para permitir este acesso:

* Os servidores Linux geralmente adicionam ``no_root_squash`` ao caminho exportado em ``/etc/exports`` .
* Os sistemas NetApp e EMC normalmente controlam o acesso com regras de exportação que estão ligadas a endereços ou redes IP específicos.

Se utilizar as regras de exportação, lembre-se de que a cache pode utilizar vários endereços IP diferentes a partir da sub-rede cache. Permitir o acesso a partir de toda a gama de possíveis endereços IP da sub-rede.

> [!NOTE]
> Embora a cache precise de acesso raiz ao sistema de armazenamento back-end, pode restringir o acesso aos clientes que se ligam através da cache. Leia [o acesso do cliente ao Control](access-policies.md#root-squash) para mais detalhes.

Trabalhe com o seu fornecedor de armazenamento NAS para permitir o nível de acesso certo para a cache.

### <a name="allow-root-access-on-directory-paths"></a>Permitir o acesso à raiz nos caminhos do diretório
<!-- linked in prereqs article -->

Para os sistemas NAS que exportam diretórios hierárquicos, a Cache Azure HPC necessita de acesso de raiz a cada nível de exportação.

Por exemplo, um sistema pode mostrar três exportações como estas:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

A exportação ``/ifs/accounting/payroll`` é uma criança ``/ifs/accounting`` de, e é em si uma ``/ifs/accounting`` criança de ``/ifs`` .

Se adicionar a ``payroll`` exportação como um alvo de armazenamento de cache HPC, a cache realmente monta ``/ifs/`` e acede ao diretório de folha de pagamento a partir daí. Assim, a Azure HPC Cache precisa de acesso raiz ``/ifs`` para aceder à ``/ifs/accounting/payroll`` exportação.

Este requisito está relacionado com a forma como o cache indexa ficheiros e evita colisões de ficheiros, utilizando alças de ficheiro que o sistema de armazenamento fornece.

Um sistema NAS com exportações hierárquicas pode dar diferentes alças de ficheiro para o mesmo ficheiro se o ficheiro for recuperado de diferentes exportações. Por exemplo, um cliente poderia montar ``/ifs/accounting`` e aceder ao ``payroll/2011.txt`` ficheiro. Outro cliente monta ``/ifs/accounting/payroll`` e acede ao ``2011.txt`` ficheiro. Dependendo da forma como o sistema de armazenamento atribui as alças de ficheiros, estes dois clientes podem receber o mesmo ficheiro com diferentes alças de ficheiro (uma para ``<mount2>/payroll/2011.txt`` e outra ``<mount3>/2011.txt`` para).

O sistema de armazenamento back-end mantém pseudónimos internos para alças de ficheiro, mas a Cache Azure HPC não consegue dizer quais as pegas de ficheiro na sua referência de índice o mesmo item. Assim, é possível que a cache possa ter diferentes escritos em cache para o mesmo ficheiro, e aplicar as alterações incorretamente porque não sabe que são o mesmo ficheiro.

Para evitar esta possível colisão de ficheiros para ficheiros em múltiplas exportações, a Azure HPC Cache monta automaticamente a exportação mais lenta disponível no caminho ``/ifs`` (no exemplo) e utiliza o cabo de ficheiro dado a partir dessa exportação. Se várias exportações usarem o mesmo caminho base, a Cache Azure HPC necessita de acesso raiz a esse caminho.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Ajuste as restrições de tamanho do pacote VPN
<!-- link in prereqs article and configuration article -->

Se tiver uma VPN entre o cache e o seu dispositivo NAS, a VPN pode bloquear pacotes Ethernet de 1500 byte. Poderá ter este problema se as grandes trocas entre o NAS e a instância cache Azure HPC não estiverem concluídas, mas as atualizações mais pequenas funcionarem como esperado.

Não há uma maneira simples de dizer se o seu sistema tem ou não este problema, a menos que conheça os detalhes da sua configuração VPN. Aqui estão alguns métodos que podem ajudá-lo a verificar este problema.

* Utilize farejadores de pacotes em ambos os lados da VPN para detetar quais os pacotes que transferem com sucesso.
* Se a sua VPN permitir comandos de ping, pode testar o envio de um pacote de tamanho completo.

  Executar um comando de ping sobre a VPN para o NAS com estas opções. (Utilize o endereço IP do seu sistema de armazenamento em vez do valor *<storage_IP>.)*

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Estas são as opções no comando:

  * ``-M do`` - Não se fragmente
  * ``-c 1`` - Enviar apenas um pacote
  * ``-s 1472`` - Ajuste o tamanho da carga útil para 1472 bytes. Esta é a carga útil de tamanho máximo para um pacote de 1500 bytes após a contabilização da sobrecarga do Ethernet.

  Uma resposta com êxito tem o seguinte aspeto:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se o ping falhar com 1472 bytes, há provavelmente um problema no tamanho do pacote.

Para corrigir o problema, poderá ser necessário configurar o aperto de MSS na VPN para que o sistema remoto detete corretamente o tamanho máximo do quadro. Leia a documentação dos [parâmetros IPsec/IKE do Gateway VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) para saber mais.

Em alguns casos, mudar a definição MTU para a Cache Azure HPC para 1400 pode ajudar. No entanto, se restringir o MTU na cache, também deve restringir as definições de MTU para clientes e sistemas de armazenamento back-end que interagem com a cache. Leia [configurar as definições adicionais de cache Azure HPC](configuration.md#adjust-mtu-value) para mais detalhes.

## <a name="check-for-acl-security-style"></a>Verifique o estilo de segurança ACL

Alguns sistemas NAS usam um estilo de segurança híbrido que combina listas de controlo de acesso (ACLs) com a segurança tradicional de POSIX ou UNIX.

Se o seu sistema reportar o seu estilo de segurança como UNIX ou POSIX sem incluir o acrónimo "ACL", este problema não o afeta.

Para os sistemas que utilizam ACLs, a Cache Azure HPC precisa de rastrear valores adicionais específicos do utilizador para controlar o acesso aos ficheiros. Isto é feito através da ativação de uma cache de acesso. Não existe um controlo virado para o utilizador para ligar a cache de acesso, mas pode abrir um bilhete de apoio para solicitar que seja ativado para os alvos de armazenamento afetados no seu sistema cache.

## <a name="next-steps"></a>Passos seguintes

Se tiver um problema que não foi abordado neste artigo, [abra um bilhete de apoio](hpc-cache-support-ticket.md) para obter ajuda especializada.
