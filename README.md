# MicroLab1
For the 2nd lab of Microprocessors


Κώδικας:

Στην main() ορίζουμε όλα τα setups και τα init() που πρέπει να γίνουν. Για την καταγραφή του ΑΕΜ, από την στιγμή που θα χρησιμοποιήσουμε μόνο το τελευταίο ψηφίο, ορίζουμε μια global static μεταβλητή last_value. Ορίζουμε επίσης μια global static μεταβλητή counter που θα είναι ο απαριθμητής μας. Θα χρειαστούμε δύο isr, μια για τη διαχείριση του κουμπιού και μια για την διαχείριση της uart. 
Ξεκινώντας από την uart κάθε φορά που στέλνεται ένας χαρακτήρας καλείται η uart_receive_isr η οποία θα τυπώσει αρχικά τον χαρακτήρα που στάλθηκε πίσω και έπειτα θα ελέγξει το είδος του χαρακτήρα. Αν ο χαρακτήρας είναι οποιοσδήποτε πλην του τερματικού χαρακτήρα (enter) θα κρατήσει την τιμή του στην μεταβλητή last_value , αλλιώς θα ελέγξει αν η τελευταία τιμή ήταν ζυγός ή μονός αριθμός και θα πράξει ανάλογα. Καθώς η uart στέλνει ascii, στον ελέγχο γίνονται οι κατάλληλες μετατροπές. Για τον εποπτικό έλεγχο της ρουτίνας επίσης χρησιμοποιείται το σήμα debug (P_DBG_ISR).
Στην περίπτωση που πατηθεί ο διακόπτης, η isr button_pressed_isr() θα αυξήσει τον counter και θα ελέγξει την κατάσταση της φωτοδιόδου (gpio_get(P_LED_R)) ώστε να την φέρει στην άλλη κατάσταση. Και εδώ επίσης χρησιμοποιείται ένα σήμα debug.
Για να είμαστε σίγουροι πως ο διακόπτης θα έχει υψηλότερη προτεραιότητα, τέλος, από την uart,όπως ζητείται από την εκφώνηση, ορίσαμε την προτεραιότητα της uart να είναι υψηλότερη από εκείνη του διακόπτη μέσω της ρουτίνας NVIC_SetPriority() κατά μια τιμή την οποία πήραμε από την  NVIC_GetPriority(EXTI15_10_IRQn) [EXTI15_10_IRQn =interrupt id]

Προβλήματα που αντιμετωπίσαμε:

Ένα από τα προβλήματα που αντιμετωπίσαμε ήταν το πως θα κάνουμε το led να ανάψει. Το setup του διακόπτη ήταν σημαντικό σε αυτή την περίπτωση καθώς έπρεπε να ορίσουμε σωστά το gpio_set_mode σε pullup, αφού αν ήταν απλά input υπήρχαν ορισμένες φορές που χωρίς να πατηθεί πραγματικά ο διακόπτης καλούνταν η isr, όπως και αν το θέταμε pulldown με το setup καλούνταν κατευθείαν η isr. Έπρεπε, επίσης, να θέσουμε σωστά το trigger του διακόπτη, αφού αν το θέταμε σε falling, ο διακόπτης δεν δούλευε σωστά. 
Κάτι που μας δυσκόλεψε ακόμα τέλος ήταν η εύρεση των priorities των interrupts.

Testing:

Γενικά δουλεύει αυτό που κάναμε. Στην περίπτωση που ο χρήστης δώσει χαρακτήρα 
