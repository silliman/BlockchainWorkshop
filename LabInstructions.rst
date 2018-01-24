Lab Overview
========================
In this lab, you will work with a tool called the **Hyperledger Composer Playground**.  We'll refer to it simply as *Playground*
in the lab. Playground offers a friendly user interface that allows you to develop and test your Hyperledger Composer Business
Network. Playground can connect to a live, running Hyperledger Composer Business Network deployed on an actual Hyperledger 
Fabric network.

You won't be doing that in this lab, but instead you will be using a very convenient feature of Playground- you will be 
developing and testing a Composer Business Network from your web browser. This feature allows you to get started 
right away on the business logic of a Composer Business Network without having to have an underlying Hyperledger Fabric 
network available.

Section 1: Run the Playground Tutorial from the official Hyperledger Composer online documentation.
=======

Follow the directions in the Playground Tutorial at <https://hyperledger.github.io/composer/tutorials/playground-tutorial.html>.
This will give you a chance to work with Participants, Assets and Transactions. 

When you reach the *What Next?* section at the end the tutorial, come back here and continue to *Section 2*

Section 2: Deploy and work with the trade-network
========
Try to perform as many of these tasks as you can.  Some of them may involve multiple steps.  Ask an instructor for help if you
get stuck.

1. Click the large button that reads **Deploy a new business network**

2. Scroll down until you see a large button that says **trade-network** and click it

3. On the right side of your screen, click the **Deploy** button

4. You should now see a "card" that says *admin@trade-network* near the top. Click the link at the bottom that says **Connect Now->**

5. Click the **Model File** link on the left and examine the definitions in the model

6. Click the **Script File** link on the left and you will see the Javascript functions that execute when you submit a transaction to Composer.  Composer calls these functions *transaction processor functions*.  If you do not know Javascript, that's okay,  you can get by in this lab without it.  The people in your organization who will be writing these *transaction processor functions* for your real use cases will need to know Javascript though.

7. Click the **Access Control** link on the left and you will see a default set of permissions that basically allows anybody to do anything. You would never use these definitions as is in a real use case.  You will use some more realistic definitions later in this lab.

8. Click the **Test** link near the top and click on **Trader** on the left.  Now, add three traders, one at a time, using the **Create New Participant** button in the upper left. The fields can be anything you want but you will need to use the *tradeId* field later, so give them something simple, like, *1, 2, and 3* or make the *tradeId* the same as their first name.

9. Click on **Commodity** on the left.  Now, add three commodities, one at a time, using the **Create New Asset** button in the upper left. The values can be anything, but make *tradingSymbol* something easy to remember.  As you create each asset, in the *owner* field, replace the randomly generated number to the left of the *#* symbol in *resource:org.acme.trading.Trader#5139*  with the *tradeId* of one of the Traders you created in the prior step.

10. You should now have three participants and three commodities. Next you will create an identity for each participant so that you can log on as a participant.  Currently, you are doing everything as the *admin* userid.  Click on **admin** in the upper right and then click **ID Registry** in the dropdown that appears.
11. Click **Issue New ID** in the upper right.
12. Enter a name for **ID Name**.  It can be anything, but choosing the first name of one of your participants is probably easiest. 
13. In the **Participant** field, start typing in *Trader*, and before you get too far, you will be presented with a dropdown list that shows the three Traders you defined earlier in the lab.  Choose the one that matches the *ID Name* you entered and click the **Create New** button.
14. The new ID you created shows at the top of the screen with a status of *In my wallet* and at the bottom of the screen with a status of *ISSUED*.  Move your cursor over the line at the top of the screen containing your new ID and click the **Use now** button that now appears.  You will see that the status for your ID at the bottom of the screen has changed from *ISSUED* to *ACTIVATED*. This is similar in concept to having a credit card that is issued to someone but it does not get activated until later.  (The analogy isn't perfect, since a credit card usually needs to be activated *before* first use, whereas these Composer IDs are activated *upon* first use).
15. Switch back to using the *admin* userid and then create a new ID for each of the other two participants you created.
16. Now try switching among the three new IDs and use the **Trade** transaction to change the owner of a commodity. What you can observe is that any userid can change the commodity of any owner-  in essence, they can "steal" anybody else's commodity for themselves or "give" it to somebody else.  Certainly an unrealistic scenario.  This is because of the wide open default access control list.
17. Click on the **Define** link at the top and then click on **Access Control** on the left. 
18. Place your cursor at the beginning of line 4 (the first three lines are comments) and hit **Enter** a couple times to create a couple of blank lines between the comments and the first rule.  Click your cursor at the front of the first blank line and then copy and paste these rules there:
::

 rule updateOwnCommodity {
    description: "Allow all traders to Trade only their own commodity"
    participant (p) : "org.acme.trading.Trader"
    operation: ALL
    resource (r): "org.acme.trading.Commodity"
    transaction: "org.acme.trading.Trade"
    condition: (p.getIdentifier() == r.owner.getIdentifier())
    action: ALLOW
 }


 rule readAllCommodities {
    description: "Allow all traders to see all commodities"
    participant: "org.acme.trading.Trader"
    operation: READ
    resource: "org.acme.trading.Commodity"
    action: ALLOW
 }


 rule denyUpdateOfOthersCommodities {
    description: "Deny all Traders ability to update others' Commodities"
    participant: "org.acme.trading.Trader"
    operation: ALL
    resource: "org.acme.trading.Commodity"
    action: DENY
 }

19. Click the **Update** link on the left.
20. Now, switch among the three non-admin IDs again, and this time, if you try to run the Trade transaction against a Commodity owned by somebody else, you will not be able to do so.  You will be able to run it against transactions you own. That is, you can give your commodity to others, but you can't "steal" or "redistribute" others' wealth as you see fit.

**Bonus questions and activities**

1. Although you can't update others' commodities, you can still see everybody else's commodities.  It is more realistic to expect you could see only your own commodities.  How would you change the Access Control rules to keep you from even seeing anybody else's commodities?
2. This sample business network only allows a single owner of a commodity.  It does not seem to be designed to allow multiple holders of the same commodity. How would you change the design to allow multiple holders of the same commodity?
3. There are some queries defined in this network.  We did not cover queries in this lab.  Composer queries are similar in syntax to SQL queries.  There is also a second transaction named *RemoveHighQuantityCommodities* that we did not discuss. This transaction actually executes one of these queries. See if you can find in the *Script file* where this query is called, and see if you can find in the *Query file* what this query does.
4. Queries only return results that your ID has access to.  For instance, the *admin* userid currently can see all commodities, but *if you updated the access control list as suggested in bonus question 1*, then each of the other three IDs will only see results for the commodities they own. *RemoveHighQuantityCommodities* seems to be a rather contrived transaction to begin with, as it just blindly deletes any commodities that have a quantity above 60.  If *admin* were to run this, she would be chopping people's "portfolios" indiscriminately.  If you made the changes to satisfy bonus question 1 and one of your other three IDs were to run this, they would only be shooting themselves in the foot with your updated access list-  they would only be deleting items from their own portfolio, and nobody else's!

