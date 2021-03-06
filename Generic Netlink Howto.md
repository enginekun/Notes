Generic Netlink Howto
=====================

----------

Register genl Family in kernel
------------------------------


1. 4-step procedure
    * define the family
    * define the operations
    * register the family
    * register the operations

2. define family
    * attributes enum
    * attribute policy array

        `struct nla_policy[]`

    * family structure

        `struct genl_family`

3. define operations
    * handler function

        `static int (*handler)(struct sk_buff *skb, struct genl_info *info)`

    * commands enum
    * operation structure

        `struct genl_ops`

4. register family
    * code

        `genl_register_family(&xxx_genl_family);`

5. register operations
    * code

        `genl_register_ops(&xxx_genl_family, &xxx_genl_ops);`


Send & Receive genl message in kernel
=====================================

1. 3-step process
    * allocate message buffer
    * create message
    * send

2. allocate message
    * code

        `struct sk_buff *skb;`
        `skb = genlmsg_new(NLMSG_GOODSIZE, GFP_KERNEL);`

3. create message
    * code

            int rc;
            void *msg_head;
            /* create the message headers */
            msg_head = genlmsg_put(skb, pid, seq, type, 0, flags, XXX_CMD, 1);
            if (msg_head == NULL) {
                rc = -ENOMEM;
                goto failure;
            }
            /* add a DOC_EXMPL_A_MSG attribute */
            rc = nla_put_string(skb, DOC_EXMPL_A_MSG, "Generic Netlink Rocks");
            if (rc != 0)
                goto failure;
            /* finalize the message */
            genlmsg_end(skb, msg_head);
    

4. send
    * code

            genlmsg_unicast(skb, pid);
  

Using genl in userspace
-----------------------
