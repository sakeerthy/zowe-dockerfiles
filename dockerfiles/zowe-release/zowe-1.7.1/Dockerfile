FROM loyaltyone/docker-slim-java-node

#zlux-server.httpsPort=8544
EXPOSE 8544/tcp 
#gatewayPort=7554  
EXPOSE 7554/tcp 
#discoveryPort=7553   
EXPOSE 7553/tcp 
#catalogPort=7552
EXPOSE 7552/tcp 
#zos-services.jobsAPIPort=8545
EXPOSE 8545/tcp
#zos-services.mvsAPIPort=8547
EXPOSE 8547/tcp
#fakeOSMF/ZOWE_ZOSMF_PORT=1443
EXPOSE 1443/tcp 

ENV NODE_HOME='/usr/local'
ENV ZOWE_JAVA_HOME='/usr'

#if ZOWE_ZOSMF_HOST is empty fakeOSMF is used instead
ENV ZOWE_ZOSMF_HOST=''
ENV ZOWE_ZOSMF_PORT='1443'
ENV ZOWE_ZSS_HOST='zss'
ENV ZOWE_ZSS_PORT='8542'

ENV LAUNCH_COMPONENT_GROUPS='GATEWAY,DESKTOP'

#ENV PAX_FILE=$(wget "https://gizaartifactory.jfrog.io/gizaartifactory/libs-release-local/org/zowe/nightly/" -qO - | grep zowe- | cut -d'"' -f2 | tail -n 1)
ENV PAX_FILE=zowe-1.7.0-staging-346-20191204051355.pax

RUN apt-get update && apt-get install -y --no-install-recommends jq moreutils pax openjdk-8-jdk-headless mc wget && rm -rf /var/lib/apt/lists/*

RUN echo "dash dash/sh boolean false" | debconf-set-selections
RUN DEBIAN_FRONTEND=noninteractive dpkg-reconfigure dash


RUN mkdir /root/zowe/ && wget -q "https://d1xozlojgf8voe.cloudfront.net/builds/1.7.1/zowe-1.7.1.pax" -O /root/zowe/zowe.pax

RUN cd /root/zowe/ && \    
    ( echo '#!/bin/sh'; echo 'tr '"'"'\000\001\002\003\004\005\006\007\010\011\012\013\014\015\016\017\020\021\022\023\024\025\026\027\030\031\032\033\034\035\036\037\040\041\042\043\044\045\046\047\050\051\052\053\054\055\056\057\060\061\062\063\064\065\066\067\070\071\072\073\074\075\076\077\100\101\102\103\104\105\106\107\110\111\112\113\114\115\116\117\120\121\122\123\124\125\126\127\130\131\132\133\134\135\136\137\140\141\142\143\144\145\146\147\150\151\152\153\154\155\156\157\160\161\162\163\164\165\166\167\170\171\172\173\174\175\176\177\200\201\202\203\204\205\206\207\210\211\212\213\214\215\216\217\220\221\222\223\224\225\226\227\230\231\232\233\234\235\236\237\240\241\242\243\244\245\246\247\250\251\252\253\254\255\256\257\260\261\262\263\264\265\266\267\270\271\272\273\274\275\276\277\300\301\302\303\304\305\306\307\310\311\312\313\314\315\316\317\320\321\322\323\324\325\326\327\330\331\332\333\334\335\336\337\340\341\342\343\344\345\346\347\350\351\352\353\354\355\356\357\360\361\362\363\364\365\366\367\370\371\372\373\374\375\376\377'"'"' '"'"'\000\001\002\003\234\011\206\177\227\215\216\013\014\015\016\017\020\021\022\023\235\012\010\207\030\031\222\217\034\035\036\037\200\201\202\203\204\205\027\033\210\211\212\213\214\005\006\007\220\221\026\223\224\225\226\004\230\231\232\233\024\025\236\032\040\240\342\344\340\341\343\345\347\361\242\056\074\050\053\174\046\351\352\353\350\355\356\357\354\337\041\044\052\051\073\136\055\057\302\304\300\301\303\305\307\321\246\054\045\137\076\077\370\311\312\313\310\315\316\317\314\140\072\043\100\047\075\042\330\141\142\143\144\145\146\147\150\151\253\273\360\375\376\261\260\152\153\154\155\156\157\160\161\162\252\272\346\270\306\244\265\176\163\164\165\166\167\170\171\172\241\277\320\133\336\256\254\243\245\267\251\247\266\274\275\276\335\250\257\135\264\327\173\101\102\103\104\105\106\107\110\111\255\364\366\362\363\365\175\112\113\114\115\116\117\120\121\122\271\373\374\371\372\377\134\367\123\124\125\126\127\130\131\132\262\324\326\322\323\325\060\061\062\063\064\065\066\067\070\071\263\333\334\331\332\237'"'"' <$1' ) >/bin/conv && chmod +x /bin/conv && \
    rm -rf zowe && \
    mkdir zowe && \
    cd zowe && \
    tar -xvf ../zowe.pax --strip 1 && \
    find . -type f -iregex '.*\.\(rexx\|js\|sh\|json\|jcl\|yaml\|clist\|env\)$' -exec sh -c "conv '{}' | sponge '{}'" \; && \
    find . -type f -name '*.sh' -exec sh -c "sed -i 's/-Xquickstart//' {}" \; && \
    find . -type f -name '*.sh' -exec sh -c "sed -i 's/-ppx/-pp/' {}" \; && \
    find . -type f -name '*.sh' -exec sh -c "sed -i 's/iconv -f IBM-1047 -t IBM-850/cat/' {}" \; && \
    find . -type f -name '*.sh' -exec sh -c "chmod +x {}" \; && \
    sed -i 's/-px //' scripts/zowe-install-zlux.sh && \
    \
    sed -i 's/java version/openjdk version/' scripts/utils/validate-java.sh && \    
    \
    echo "exit 0" > scripts/opercmd && \
    echo "exit 0" > /bin/extattr && chmod +x /bin/extattr && \
    echo "exit 0" > /bin/ping && chmod +x  /bin/ping && \
    echo "exit 0" > /bin/oping && chmod +x  /bin/oping && \
    echo "exit 0" > /bin/onetstat && chmod +x  /bin/onetstat && \
    echo "exit 0" > /bin/chtag && chmod +x  /bin/chtag && \    
    echo 'cat $5' > /bin/iconv && chmod +x /bin/iconv && \    
    mv /usr/bin/iconv /usr/bin/iconv_ 

#Conver content of pax files from ebcdic to ASCII
RUN cd /root/zowe/zowe/files && \
    for f in *.pax; do \
        echo "Processing $f file." && \
        rm -rf tmp && \
        mkdir tmp && \
        cd tmp && \
        pax -rf ../$f && \
        rm ../$f && \
        find . -type f -iregex '.*\.\(rexx\|js\|sh\|json\|jcl\|yaml\|clist\|html\|template\|css\|svg\|map\|gz\|env\)$' -exec sh -c "conv '{}' | sponge '{}'" \; && \
        find . -type f -name '*.sh' -exec sh -c "sed -i 's/-Xquickstart//' {}" \; && \
        pax -wf ../$f . && \
        cd .. ;  \
    done && \
    \
    cd zlux && \
    for f in *.pax; do \
        echo "Processing $f file." &&\
        rm -rf tmp &&\
        mkdir tmp &&\
        cd tmp &&\
        pax -rf ../$f &&\
        rm ../$f &&\
        find . -type f -exec sh -c "conv '{}' | sponge '{}'" \; &&\
        find . -type f -name '*.sh' -exec sh -c "sed -i 's/-Xquickstart//' {}" \; &&\
        pax -wf ../$f . &&\
        cd .. ; \
    done && \
    rm -f /root/zowe/*.pax 

RUN cd /root/zowe/ && \
    export PATH=$PATH:$NODE_HOME/bin && \    
    cd zowe/install && \
    sed 's/\(rootDir=.*\/\).*/\1current/g' -i zowe-install.yaml && \
    bash zowe-install.sh -I && \
#    return && \
    rm -f /root/zowe/*.pax && rm -rf /root/zowe/zowe/ && \
    apt-get purge -y pax && apt-get autoremove -y && rm -rf /var/lib/apt/lists/* && \
    easy_install flask && easy_install httpie


#hotfix setup
COPY hotfix /root/zowe/hotfix
RUN chmod o+rx /root/zowe/hotfix/* && mv /root/zowe/hotfix/setup-apiml-certificates-template.sh /root/zowe/current/components/api-mediation/scripts

# setup fakeOSMF
COPY tools /root/zowe/tools
RUN chmod o+rx /root/zowe/tools/*

ENTRYPOINT ["/root/zowe/tools/run.sh"]
