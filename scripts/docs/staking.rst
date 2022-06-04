


  genz-cardano-node \
      staking generateOperationalCertificate \
      --tip LIVE

      
      
      generateRegistrationCertificates
      
       --reward-wallet
       --owners
       --metadata-url
       --relay-ip                              PUBLIC_RELAY_IP; TOPOLOGY,PUBLIC,
       --pool-pledge                           
       --pool-cost                             
       --pool-margin                           

   genz-cardano-node \
      staking generateRegistrationCertificates \
      --reward-wallet main \
      --owners genz,wallet2,wallet3 \
      --relay-ip TOPOLOGY \
      --pool-cost 340 \
      --pool-margin 0.01 \
      --metadata-url https://raw.githubusercontent.com/genz-pool/genz-pool.github.io/main/metadata-cardano.json
