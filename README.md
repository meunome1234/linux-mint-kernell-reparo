🧰 Reparo do Kernel Panic no Linux Mint
Diagnóstico e solução completa para erro: VFS: Unable to mount root fs
📖 Descrição
Este relatório documenta o processo de diagnóstico e reparo de um problema crítico de inicialização no Linux Mint, causado por falha de compilação do módulo xone via DKMS durante a instalação do kernel 6.14.0-34-generic. O objetivo é servir como referência técnica direta para quem enfrentar o mesmo erro.
🧩 Sistema Afetado
- Distro: Linux Mint (base Ubuntu)
- Kernel com falha: 6.14.0-34-generic
- Kernel estável anterior: 6.14.0-33-generic
- Causa raiz: falha de compilação via DKMS (driver xone)
- Sintoma: initramfs corrompido → partição raiz não montada
🧠 Diagnóstico
O erro ocorreu devido a uma falha na recompilação do módulo xone durante a atualização de kernel. O DKMS entrou em loop de erro, deixando o initramfs incompleto e impedindo a montagem da raiz (/dev/sde3).
🛠️ Fase 1 — Reparo do Boot (via Live USB)
1. Montagem do sistema:
sudo mount /dev/sde3 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /sys /mnt/sys
sudo mount --bind /proc /mnt/proc
sudo mount --bind /run /mnt/run
2. Acesso ao ambiente chroot:
sudo chroot /mnt
3. Remoção do kernel quebrado:
dpkg --list | grep linux-image
apt purge linux-image-6.14.0-34-generic linux-headers-6.14.0-34-generic
4. Reinstalação do GRUB:
grub-install /dev/sde
update-grub
5. Reinicialização:
exit
sudo umount -R /mnt
sudo reboot
🔧 Fase 2 — Estabilização Permanente
1. Identificação da causa: o driver xone (via DKMS) foi confirmado como fonte da falha.
2. Remoção do DKMS:
sudo apt purge dkms
3. Correção do repositório:
Antigo mirror: mirror.ufam.edu.br
Novo mirror: mirror.ufscar.br
4. Atualização e reinstalação limpa do kernel:
sudo apt update
sudo apt install --reinstall linux-image-generic linux-headers-generic
sudo update-initramfs -u
sudo update-grub
✅ Resultado Final
- Linux Mint voltou a iniciar normalmente
- Kernel 6.14.0-34-generic reinstalado com sucesso
- Nenhum erro de VFS ou kernel panic após reinicializações
- SSD de 240 GB removido da BIOS como dispositivo de boot
- Sistema estável e atualizado
💾 Pós-Reparo (Recomendado)
1. Criar ponto de restauração:
sudo timeshift --create --comments 'Sistema estável após reparo de kernel'

2. Manter apenas kernels testados e funcionais.
3. Evitar reinstalar o xone via DKMS até correção oficial.
🏷️ Tags
linux-mint · kernel-panic · initramfs · dkms · grub · xone-driver · ubuntu-based
📅 Histórico
Data do incidente: Outubro–Novembro de 2025
Sistema restaurado por: Matheus Soares
Licença: CC BY-SA 4.0 — pode ser copiado e adaptado com atribuição.
