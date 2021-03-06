Os arquivos de configuração do _systemd_  residem em `/lib/systemd/system/`, este é o local padrão para os arquivos que vem com o sistema operacional. Se precisar modificar alguns desses arquivos ou criar os próprios o diretório indicado é o `/etc/systemd/system`, os arquivos neste diretório tem precedência de execução sobre o `/lib/systemd/system/`.

No diretório `/lib/systemd/system/` há varios tipos de arquivos:

* services
* socket
* target
* mount e automount
* timer

Os arquivos executáveis do systemd ficam em `/lib/systemd/`, porém não interagimos diretamente com eles, para isso há o utilitário `systemctl`.

Para visualizar as unidades ativas do _systemd_ podemos rodar o seguinte comando:

`$ systemctl list-units`

Para ver as unidades ativas e inativas:

`$ systemctl list-units --all`

Para ver tipos específicos de unidades é utilizado a opção `-t`

`$ systemctl list-units -t service`

Para visualizar os serviços inativos

`$ systemctl list-units -t service --state=dead`

Para ver os arquivos _units_ instalados na máquina

`$ systemctl list-unit-files`

```
$ systemctl list-unit-files                                                       ─╯
UNIT FILE                                  STATE           VENDOR PRESET
proc-sys-fs-binfmt_misc.automount          static          -            
-.mount                                    generated       -            
boot-efi.mount                             generated       -            
boot.mount                                 generated       -            
dev-hugepages.mount                        static          -            
dev-mqueue.mount                           static          -            
home.mount                                 generated       -            
proc-fs-nfsd.mount                         static          -            
proc-sys-fs-binfmt_misc.mount              disabled        disabled     
run-media-sergio-WD2TB.mount               generated       -            
run-vmblock\x2dfuse.mount                  disabled        disabled     
sys-fs-fuse-connections.mount              static          -            
sys-kernel-config.mount                    static          -            
```

* os _unit-files_ com status _generated_ são criados automaticamente quando o systemd lê o arquivo `/etc/fstab/`;
* os _unit-files_ com status _static_ não podem ser habilitados ou desabilitados;
* os _unit-files_ com status _disabled_ ou _enabled_ estão habilitados ou desabilitados na inicialização do sistema

Para ver se um único serviço está habilitado

`$ systemctl is-enabled avahi-daemon`

Para verificar se um serviço está ativo

`$ systemctl is-active avahi-daemon`

Para startar um serviço:

`# systemctl start httpd`

Para verificar o status de um serviço:

`# systemctl status httpd`

Para parar um serviço:

`# systemctl stop httpd`

Para habilitar um serviço na inicialização:

`# systemctl enable httpd`

Para habilitar na inicialização e startar um serviço:

`# systemctl enable --now httpd`

Quando habilitamos um serviço é criado um link simbólico dentro de `/etc/systemd/system/multi-user.target.wants`que aponta para o arquivo _httpd.service_.

Para desabilitar um serviço da inicialização:

`# systemctl disable httpd`

ou

`# systemctl disable --now httpd`

Também é possível matar um processo com o _systemd_

`# systemctl kill httpd`

Também é possível matar um processo enviando um sinal para este, por default é o SIGTERM (15), mas é possível especificar um outro sinal com a opção `-s`

`# systemctl kill -s SIGKILL httpd`

Para mascarar um serviço e impedir que ele seja habilitado:

`# systemctl mask httpd`

Quando um serviço é mascarado é criado um lik simbólico apontando para `/dev/null`.

Para ver a configuração real em execução do _systemd_

`# systemcl show`

A configuração global está em `/etc/systemd/system.conf`

### Unit Services

As _unit services_ são equivalentes aos scripts de inicialização do SysV. Para ler sobre todas as descrições e parâmetros que podem ser utilizados no _unit services_ consulte a página de manual com `man systemd.directives`.

### Unit Sockets

Permite a comunicação entre os processos na própria máquina e também entre processos executados em hosts distintos com o nosso.

### Unit Path

Indica ao _systemd_ para monitorar um determinado arquivo ou diretório para ver quando ele muda. Quando o _systemd_ detectar alteração o serviço será ativado.

Para editar ou criar um serviço a forma mais apropriada é dentro do `/etc/systemd/system/`. Pode-se editar o arquivo com nano/vim ou usar a função de edição do _systemctl_.

`systemctl --force --full edit apache2`

Linha acrescentada na seção [Install] do arquivo `/etc/systemd/system/apache.service` para poder utilizar _httpd_ no lugar de _apache2_. Essa configuração só serve para derivados do Debian. Nos sistemas derivados do Red Hat está por default.

[Install]
Alias=httpd.service

Sempre que você modificar ou adicionar um arquivo de serviço, precisará fazer um `# systemctl daemon-reload`

Agora é preciso desabilitar o serviço e habilita-lo novamente:

`# systemctl disable apache2`

```
# systemctl enable apache2
Synchronizing state of apache2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apache2
Created symlink /etc/systemd/system/httpd.service → /etc/systemd/system/apache2.service.
Created symlink /etc/systemd/system/multi-user.target.wants/apache2.service → /etc/systemd/system/apache2.service.
```

Observe que foi criado o seguite link `Created symlink /etc/systemd/system/httpd.service → /etc/systemd/system/apache2.service.` e agora podemos usar _httpd_.

Para visualizar todo arquivo de configuração de um serviço podemos usar

`# systemctl cat apache2`
