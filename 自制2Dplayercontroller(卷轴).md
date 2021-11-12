# 2D卷轴任务控制脚本

`using System.Collections;`
`using System.Collections.Generic;`
`using UnityEngine;`

`public class PlayerController : MonoBehaviour`
`{`
    `private Rigidbody2D Rb;`
    `private Animator Anim;`
    `public float speed;`
    `public float jumpForce;`
    `float xVelocity;`
    `public bool isOnGround;`
    `public float checkRadius;`
    `public LayerMask ground;`
    `public GameObject groundCheck;`
    `private int jumpTimes;`
    `private bool canJump=true;`
    `void Start()`
    `{`
        `Rb = GetComponent<Rigidbody2D>();`
        `Anim = GetComponent<Animator>();`
        `jumpTimes = 1;`
    `}`

    // Update is called once per frame
    void Update()
    {
        isOnGround = Physics2D.OverlapCircle(groundCheck.transform.position, checkRadius, ground);
        Movement();
        AnimationSwitch();
    }
    void Movement()
    {
        xVelocity = Input.GetAxisRaw("Horizontal");
        Rb.velocity = new Vector2(xVelocity * speed, Rb.velocity.y);
        if (xVelocity != 0)
        {
            transform.localScale = new Vector3(xVelocity, 1, 1);
        }
        if (Input.GetKeyDown("j") && canJump) 
        {
            Rb.velocity = new Vector2(Rb.velocity.x, jumpForce);
            jumpTimes=jumpTimes-1;
        }
        if (jumpTimes == 0)
        {
            canJump = false;
        }
        else
        {
            canJump = true;
        }
        if (isOnGround)
        {
            jumpTimes = 1;
        }
    }
    void AnimationSwitch()
    {
        Anim.SetFloat("speed", Mathf.Abs(Rb.velocity.x));
        Anim.SetBool("jumping", !isOnGround);
        if (!isOnGround && Rb.velocity.y < 0)
        {
            Anim.SetBool("falling", true);
        }
        else
        {
            Anim.SetBool("falling", false);
        }
    }
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.CompareTag("Spike"))
        {
            Anim.SetTrigger("isdead");
        }
    }
    public void Death()
    {
        Destroy(Rb.gameObject);
    }
}

